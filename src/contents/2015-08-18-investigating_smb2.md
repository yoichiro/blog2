---
layout: post
status: publish
published: true
title: SMB2探検記
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 3315
wordpress_url: https://www.eisbahn.jp/yoichiro/?p=3315
date: '2015-08-18 09:44:23 +0900'
date_gmt: '2015-08-18 00:44:23 +0900'
categories:
- SMB/CIFS
---

以下の内容は、SMB2の調査をしていた時のメモです。

---

SMB1/CIFSはほぼ制覇したと思っているので、そろそろSMB2についての調査を開始する。盗聴は、macosxとSamba間の通信に対して行った。

まずは、ネゴシエートがどう変わっているか、調査開始。例によって、Wiresharkを使って盗聴した結果を見ていく。macosxが最初に何を送っているかというと、なんとSMB1/CIFSのパケットだった。指定ダイアレクトとして、"NT LM 0.12"だけでなく、"SMB 2.002”と"SMB 2.???"の2つも送っているっぽい。で、そのレスポンスとして、どうやらSMB2のパケットを送ることで、SMB2で通信できることをサーバがクライアントに示す、ということっぽい。

SMB2のパケットは、SMB1/CIFSの時と同じく、0x00 + Length(3bytes) + Message、という構造。ここだけビッグエンディアンなところも一緒。そして、SMB2のヘッダは、0xFFで始まるのではなく、0xFEで始まってた。まず、ヘッダの構造は以下のようになってるっぽい。

```
SMB2_HEADER_REQUEST {
  UCHAR[4] server_component; // 0xFE 0x53 0x4d 0x42
  USHORT structure_size; // 64
  USHORT credit_charge; // 1
  USHORT channel_sequence; // 0
  USHORT reserved; // 0
  USHORT command;
  USHORT credits_requested; // 1 or 256
  UINT flags;
  UINT chain_offset; // 0
  ULONG message_id;
  UINT process_id;
  UINT tree_id;
  ULONG session_id;
  UCHAR[16] signature; // 0
}
```

```
SMB2_HEADER_RESPONSE {
  UCHAR[4] server_component; // 0xFE 0x53 0x4d 0x42
  USHORT structure_size; // 64
  USHORT credit_charge;
  UINT status; // NT_STATUS
  USHORT command;
  USHORT credits_granted;
  UINT flags;
  UINT chain_offset;
  ULONG message_id;
  UINT process_id;
  UINT tree_id;
  ULONG session_id;
  UCHAR[16] signature; // 0
}
```

credit_chargeは、ずっと1が指定されていた。channel_sequenceは、ずっと0。credits_requestedは、1の時と256の時があるっぽい。どういうときに1になって、どういうときに256になるのか、よくわからない。message_idは、1からの連番を振っていけば良さそう。

credits_requestedは仕様書だと「On a request, this field indicates the number of credits the client is requesting.」と書かれている。つまり、クライアントが要求しているcreditの数、となる。そもそもcreditって何かというと、

credit: A value that is granted to an SMB 2 Protocol client by an SMB 2 Protocol server that limits the number of outstanding requests that a client can send to a server.
クレジット: クライアントがサーバに送信可能な未処理の要求の数を制限するSMB2プロトコルサーバによってSMB2プロトコルクライアントに与えられる値。

という定義っぽい。

コマンド、credits_requested、credits_grantedの関係を調べてみた。

```
NEGOTIATE Response: credits_granted=1
SESSION_SETUP Request: credits_requested=256
SESSION_SETUP Response: credits_granted=1
SESSION_SETUP Request: credits_requested=256
SESSION_SETUP Response: credits_granted=33
TREE_CONNECT Request: credits_requested=1
TREE_CONNECT Response: credits_granted=1
TREE_CONNECT Request: credits_requested=1
TREE_CONNECT Response: credits_granted=1
CREATE Request: credits_requested=256
CREATE Response: credits_granted=33
IOCTL Request: credits_requested=256
IOCTL Response: credits_granted=33
IOCTL Request: credits_requested=256
IOCTL Response credits_granted=33
CLOSE Request: credits_requested=256
CLOSE Response: credits_granted=33
TREE_DISCONNECT Request: credits_requested=256
TREE_DISCONNECT Response: credits_granted=33
TREE_DISCONNECT Request: credits_requested=256
TREE_DISCONNECT Response: credits_granted=33
SESSION_LOGOFF Request: credits_requested=1
SESSION_LOGOFF Response: credits_granted=1
```

credits_requestedに1を指定した場合、必ずcredits_grantedも1が返ってきている。しかし、credits_requestedに256を指定した場合、ほとんどcredits_grantedに33が返ってきているのに、SESSION_SETUPの1回目だけ1が返ってきてる。なんだか良くわからない。結局のところ、もしかしたら並列でリクエストを送ったりしないのであれば、credits_requestedはずっと1を指定しておけば良さそうだけど、どうなんだろう？

chain_offset(仕様書だとnext_command)は、compounded requestという場合に指定する値っぽい。1リクエスト内で複数のコマンドを送りたい場合のオフセットっぽい気がする。compound requestではない場合は、0で良さそう。

SMB1/CIFSでは、ユーザ認証の結果はuser_idという値で扱っていた。SMB2の場合は、その役目はsession_idが担っているっぽい。ユーザ認証前は0で、ユーザ認証後はSMB2_SESSION_SETUPの結果値を指定する。

SMB2でのコマンドの一覧は、別のノートに書いておいた。

SMB2ヘッダでのflags値は、SMB1/CIFSの時よりも単純。最初の1ビットが送受信の向き（0:Request, 1:Response）で、AsyncでもなくChainedでもなくSignedでもなくDFS処理でもなくReplayでもなければ、最初の1ビット以外は全部0で良い。

さて、ネゴシエートのレスポンスの構造は、以下のようになっていた。

```
SMB2_NEGOTIATE_RESPONSE {
  USHORT structure_size;
  UCHAR security_mode; // 0x01:署名可能 0x02:署名必須
  USHORT dialect_revision;
  UCHAR[16] server_guid;
  UINT capabilities;
  UINT max_transaction;
  UINT max_read_size;
  UINT max_write_size;
  ULONG system_time; // SMB Timestamp
  ULONG server_start_time; // SMB Timestamp
  USHORT security_buffer_offset;
  USHORT security_buffer_length;
  ANY buffer;
}
```

SMB1/CIFSと違って、サーバが選択したダイアレクトは、インデックス指定ではなく、ダイアレクトの番号（SMB2.0.2なら0x0202）という指定で返ってくる。そして、capabilitiesは、もしダイアレクトがSMB2.0.2の場合は、ほとんどのビットが意味がないっぽい。あとは、SMB1/CIFSの時とそれほど変わらない感じ。

そういえば、wordsとかbytesとかなくなってる。これは良い感じ。

ネゴシエートができた後は、ユーザ認証する。そのために、SMB2 SESSION_SETUPコマンドを使用する。見てみると、SMB1/CIFSとほとんど同じっぽい。つまり、SESSION_SETUPメッセージが、Type 1〜3 Messageを運ぶ構図に変わりはない。SESSION_SETUPリクエストの構造は以下。

```
SESSION_SETUP_REQUEST {
  USHORT structure_size;
  UCHAR flags; // 0
  UCHAR security_mode; // 1=Signing enabled:True
  UINT capabilities; // 1=DFS
  UINT channel; // 0
  USHORT security_blob_offset;
  USHORT security_blob_length;
  ULONG previous_session_id; // 0
  ANY security_blob;
}
```

security_blobにType 1 Messageを乗せてサーバに送ると、SMB2 SESSION_SETUPレスポンスが返ってくる。このレスポンスにて以下が得られるのも、SMB1/CIFSと同じ。

ヘッダのstatus値に、MORE_PROCESSING_REQUIRED (0xc0000016)が入ってる。
ヘッダのsession_id値に、Session IDが入ってる。

SESSION_SETUPレスポンスの構造は以下。

```
SESSION_SETUP_RESPONSE {
  USHORT structure_size;
  USHORT session_flags; // 0x01:Guest 0x02:NULL
  USHORT security_blob_offset;
  USHORT security_blob_length;
  ANY security_blob;
}
```

すごい単純。マジでSMB1/CIFSのあの複雑さはなんだったのか。。。security_blobにType 2 Messageが入ってくる。あとは、Type 3 Messageを作って、SESSION_SETUPリクエストに乗せて再度サーバに送って、SESSION_SETUPレスポンスのstatus値が0であれば、ユーザ認証完了。

・・・と思ったけど、どうやらSMB1/CIFSにはない新しい概念を覚えなければならないっぽい。それは、GSS-API、SPNEGO。Type 1〜3 Messageが直接Security Blobに入っているわけではなく、それらはGSS-API、SPNEGOとして送られるっぽい。

GSS-APIのRFC的なものを見つけた。

[https://www.ietf.org/rfc/rfc4178.txt](https://www.ietf.org/rfc/rfc4178.txt)

これによると、以下のような決まりがあるっぽい。

```
0x30 + UCHAR (identifier octet for constructed SEQUENCE + length)
0xa0 + UCHAR (identifier octet for constructed [0] + length)
0x06 + UCHAR + ANY (identifier octet for primitive OBJECT IDENTIFIER + length + value)
```

もうちょっと調べたら、以下のサイトを見つけた。上記よりも詳しく書いてありそう。

[https://msdn.microsoft.com/en-us/library/ms995330.aspx](https://msdn.microsoft.com/en-us/library/ms995330.aspx)

まず、DER Types。DERとは”Distinguished Encoding Rules”の略っぽい。これは、ASN.1オブジェクトをバイナリ変換する際に適用されるルールらしい。DERはBERのサブセットで、X.509で定義されたものっぽい。書式としては、

タグフィールド + 値の長さフィールド + 値フィールド

という感じになる。これは以下のサイトから得た知識。

[http://www.geocities.co.jp/SiliconValley-SanJose/3377/](http://www.geocities.co.jp/SiliconValley-SanJose/3377/)

じゃあASN.1って何？ってなるんだけど、正式には”Abstract Syntax Notation One”というらしい。「電気通信やコンピュータネットワークでのデータ構造の表現・エンコード・転送・デコードを記述する標準的かつ柔軟な記法」ということっぽい。このASN.1をどう符号化するか、というものの一つがDER。ご丁寧にWikipediaのASN.1のページに、DERを使った符号化が書いてあった。

[https://ja.wikipedia.org/wiki/Abstract_Syntax_Notation_One](https://ja.wikipedia.org/wiki/Abstract_Syntax_Notation_One)

```
FooQuestion ::= SEQUENCE {
    trackingNumber INTEGER,
    question VisibleString
}
```

という定義があって、

```
myQuestion FooQuestion ::= {
    trackingNumber 5,
    question "Anybody there?"
}
```

という値があったら、これをDERで符号化すると、

```
0x30 - タグ SEQUENCE
0x13 - 長さ
0x02 - タグ INTEGER
0x01 - 長さ
0x05 - 値 (trackingNumber)
0x1a - タグ VisibleString
0x0e - 長さ
0x416e79626f792074686572653f - 値 (question)
```

となるらしい。だんだんわかってきた。最初にタグのバリエーションがどれだけあるか気になる。探すと、MSDNにそれっぽいものがあった。

[https://msdn.microsoft.com/ja-jp/library/windows/desktop/bb648642(v=vs.85).aspx](https://msdn.microsoft.com/ja-jp/library/windows/desktop/bb648642(v=vs.85).aspx)

なんと、タグの値には、各ビットにちゃんと意味があった。

![IC262327](https://www.eisbahn.jp/yoichiro/images/2015/08/IC262327.png)

上記のページに載っていたタグ値は以下。

```
BOOLEAN - 0x01
INTEGER - 0x02
BIT STRING - 0x03
OCTET STRING - 0x04
NULL - 0x05
OBJECT IDENTIFIER - 0x06
UTF8String - 0x0c
PrintableString - 0x13
TeletextString - 0x14
IASString - 0x16
BMPString - 0x1e
SEQUENCE, SEQUENCE OF - 0x30
SET, SET OF - 0x31
```

「何個Stringあんねん！」って感じ。そして、VisibleString (0x1a)が上記の表示にないことに気がついた。また不完全なリストかよ・・・と更に調べてみると、どうやらこれらのタグ値は、X.690で定義されているものだ、ということがわかった。

[https://en.wikipedia.org/wiki/X.690](https://en.wikipedia.org/wiki/X.690)

次の長さについては、長さ自体がどれだけ長いかによって指定方法が変わってくる。

```
長さが127以下の場合 - 長さをそのまま1バイトで指定する。(ex. 0x0b)
長さが128〜255の場合 - 0x81 + 長さを1バイトで指定する。(ex. 0x81 0xfe)
長さが256〜65535の場合 - 0x82 + 長さを2バイトで指定する。(ex. 0x82 0x05 0xbc)
長さが不定の場合 - 0x80 + 値 + 0x0000 で指定する。
```

ASN.1 DERの規定によると、長さはどうやらビッグエンディアンで指定するように規定されているらしい。

SMB2でのパケットキャプチャ結果を見てみると、0x60で始まってる場合と、0xa1で始まってる場合があった。まず、0x60の方は結構簡単に何かわかった。例えば、以下のサイトで説明されていた。

[http://docs.oracle.com/cd/E19476-01/821-0510/def-basic-encoding-rules.html](http://docs.oracle.com/cd/E19476-01/821-0510/def-basic-encoding-rules.html)

0x60は、BERで定義されているものっぽい。そしてその意味は、[APPLICATION 0] SEQUENCEらしい。しかし、0xa1がなかなか見つからない。で、結局「ber 0xa1」で検索したら、Oracleのサイトでソースコードがヒットした。

[https://community.oracle.com/thread/1157421](https://community.oracle.com/thread/1157421)

OpenLdapBERTagDecoderというJavaのクラスで、case文でがっつりとタグ値が並んでた。コメントで各値の説明も書いてある。

```
0x60: /* [APPLICATION 0] For Bind Request */
0x61: /* [APPLICATION 1] Bind Response */
0x63: /* [APPLICATION 3] Search Request
       * If doing search without bind first,
       * x500.arc.nasa.gov returns tag [APPLICATION 3]
       * in Search Response. Gee.
       */
0x64: /* [APPLICATION 4] Search Response */
0x65: /* [APPLICATION 5] Search Result */
0x67: /* [APPLICATION 7] Modify Response */
0x69: /* [APPLICATION 9] Add Response */
0x6a: /* [APPLICATION 10] Del Request */
0x6b: /* [APPLICATION 11] Del Response */
0x6d: /* [APPLICATION 13] ModifyRDN Response */
0x6f: /* [APPLICATION 15] Compare Response */
0x78: /* [APPLICATION 23] Extended Response */
0x73: /* [APPLICATION 19] SearchResultReference */
    element = new BERSequence(decoder, stream, bytesRead);
    implicit[0] = true;
    break;
0x80: /* [APPLICATION 16] 64+16 */
    element = new BERInteger(stream, bytesRead);
    implicit[0] = true;
    break;
/* 16/02/97 MS specific */
0x85: /* Context Specific [5]:
       * (a) Handle Microsoft v3 referral bugs! (Response)
       * (b) Handle Microsoft v3 supportedVersion in Bind
       *     response
       */
    element = new BERInteger(stream, bytesRead);
    implicit[0] = true;
    break;
0x87: /* Context Specific [7]:
       * Handle Microsoft Filter "present" in
       * search request.
       */
    element = new BEROctetString(decoder, stream, bytesRead);
    implicit[0] = true;
    break;
0x8a: /* Context Specific [10]:
       * Handle extended response
       */
    element = new BEROctetString(decoder, stream, bytesRead);
    implicit[0] = true;
    break;
0x8b: /* Context Specific [11]:
       * Handle extended response
       */
    element = new BEROctetString(decoder, stream, bytesRead);
    implicit[0] = true;
    break;
0xa3: /* Context Specific <Construct> [3]:
       * Handle Microsoft v3 sasl bind request
       */
    element = new BERSequence(decoder, stream, bytesRead);
    implicit[0] = true;
    break;
0xa7: /* Context Specific <Construct> [7]:
       * Handle Microsoft v3 serverCred in
       * bind response. MS encodes it as SEQUENCE OF
       * while it should be CHOICE OF.
       */
    element = new BERSequence(decoder, stream, bytesRead);
    implicit[0] = true;
    break;
0xa0: /* Context Specific <Construct> [0]:
       * v3 Server Control.
       * SEQUENCE of SEQUENCE of {OID  [critical] [value]}
       * THIS IS WARNING FROM PASSWORD CONTROL
       */
    element = new BERSequence(decoder, stream, bytesRead);
    implicit[0] = true;
    break;
0x81: /* Context Specific <Construct> [0]:
       * v3 Server Control.
       * SEQUENCE of SEQUENCE of {OID  [critical] [value]}
       * THIS IS ERROR FROM PASSWORD CONTROL
       */
    element = new BERInteger(stream, bytesRead);
    implicit[0] = true;
    break;
0xa1: /* Context Specific <Construct> [0]:
       * v3 Server Control.
       * SEQUENCE of SEQUENCE of {OID  [critical] [value]}
       */
    element = new BERSequence(decoder, stream, bytesRead);
    implicit[0] = true;
```

0x60は"[APPLICATION 0] For Bind Request”で、0xa1は"Context Specific 
[0]"というものらしい。どちらもSEQUENCE。

SESSION_SETUPリクエストのパケットキャプチャ結果を見てみると、上記にて定義されていない0xa2なるものも出てきていた。探してみると、またソースコードがヒットした。

[http://lists.asn1.org/pipermail/asn1/2005-May/000236.html](http://lists.asn1.org/pipermail/asn1/2005-May/000236.html)

0xa2は 「Tag for ReturnResult structure within Component (CNTX, CONS, 2)」という定義らしい。これもSEQUENCEなのだろう、きっと。

これでとりあえずGSS-APIのバイト列の読み方は何となくわかった。NEGOTIATE、SESSION_SETUPで使われていたGSS-APIのバイト列をASN.1、DERなどの仕様に基づいて解釈した結果を以下に書いておく。

```
* NEGOTIATE Response
0x60 [APPLIACTION 0] For Bind Request
  0x06 Object Identifier ::= 0x2b0601050502 (1.3.6.1.5.5.2 - SPNEGO)
  0xa0 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
    0x30 SEQUENCE
      0xa0 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
        0x30 SEQUENCE
          0x06 Object Identifier ::= 0x2b0601050502 (1.3.6.1.4.1.311.2.2.10 - NTLMSSP)
      0xa3 Handle Microsoft v3 sasl bind request
        0x30 SEQUENCE
          0xa0 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
            0x1b GeneralString ::= 0x4e4f4e45 (NONE)
```

```
* SESSION_SETUP Request (Type 1 Message)
0x60 [APPLIACTION 0] For Bind Request
  0x06 Object Identifier ::= 0x2b0601050502 (1.3.6.1.5.5.2 - SPNEGO)
  0xa0 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
    0x30 SEQUENCE
      0xa0 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
        0x30 SEQUENCE
          0x06 Object Identifier ::= 0x2b0601050502 (1.3.6.1.4.1.311.2.2.10 - NTLMSSP)
      0xa2 ReturnResult structure within Component (CNTX, CONS, 2)
        0x04 OctetString ::= Type 1 Message
```

```
* SESSION_SETUP Response (Type 2 Message)
0xa1 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
  0xa0 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
    0x30 SEQUENCE
      0xa0 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
        0x0a Enumerated ::= 1 (accept-incomplete)
      0xa1 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
        0x06 Object Identifier ::= 0x2b0601050502 (1.3.6.1.4.1.311.2.2.10 - NTLMSSP)
      0xa2 ReturnResult structure within Component (CNTX, CONS, 2)
        0x04 OctetString ::= Type 2 Message
```

```
* SESSION_SETUP Request (Type 3 Message)
0xa1 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
  0x30 SEQUENCE
    0xa2 ReturnResult structure within Component (CNTX, CONS, 2)
      0x04 OctetString ::= Type 3 Message
    0xa3 Handle Microsoft v3 sasl bind request
      0x04 OctetString ::= 0x010000009a1e2c3c1211ebb900000000
```

```
* SESSION_SETUP Response
0xa1 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
  0x30 SEQUENCE
    0xa0 SEQUENCE of SEQUENCE of {OID  [critical] [value]}
      0x0a Enumerated ::= 0 (accept-complete)
```

0x60, 0xa0〜0xa3の使い分けがいまいち謎。あと、SEQUENCEがやけに多段になってるけど、それらを気にしなければ、それほど難しくない。動的に変化することもなさそう。

ユーザ認証が終わったら、次は共有リソース一覧取得。SMB1/CIFSで最も手こずったやつ。DCE/RPCを適用することについてはきっと同じだと思うけど、その渡し方や結果の取得方法はSMB2では異なるやり方になるに違いない。

まず、SMB2 TREE_CONNECT（0x03）コマンドを使って、IPC$への接続を行い、Tree ID値を得る。TREE_CONNECTリクエストの構造は、以下。

```
TREE_CONNECT_REQUEST {
  USHORT structure_size;
  USHORT reserved; // 0
  USHORT path_offset; // ヘッダを起点にした位置
  USHORT path_length;
  ANY buffer; // UTF-16LE
}
```

パスだけ渡せば良いので簡単。TREE_CONNECTレスポンスの構造は以下。もちろん、ヘッダにTree IDがセットされる。

```
TREE_CONNECT_RESPONSE {
  USHORT structure_size;
  UCHAR share_type; // 0x02 (0x01:DISK 0x02:PIPE 0x03:PRINT)
  UCHAR reserved; // 0
  UINT share_flags; // 0x00000400 ALLOW_NAMESPACE_CACHING
  UINT capabilities; // 0
  UINT maximal_access; // 0x101f01ff
}
```

IPC$への接続なので、share_typeは0x02（PIPE）が返される。maximal_accessは、Wiresharkでは”Access Mask”と表現されていて、これは、接続した共有リソースに対しての各操作の権限があるかどうかを示すフラグ値。

しかし、macosxはIPC$への接続を2回連続して行っているっぽいんだけど、1回で済む気がする。バグ？

SMB1/CIFSの時と同じように、IPC$の接続の後は、IPC$/srvsvcのFID値を得る。これは、SMB2 CREATE（0x05）コマンドを使って行う。これは、SMB1/CIFSのNT_CREATE_ANDXコマンドのようなもの。つまり、リクエストで渡す値が多い。CREATEリクエストの構造は以下。

```
CREATE_REQUEST {
  USHORT structure_size;
  UCHAR security_flags; // 0 (Reserved)
  UCHAR requested_oplock_level; // 0 (No oplock)
  UINT impersonation_level; // 2 (Impersonation)
  ULONG smb_create_flags; // 0
  UCHAR[8] reserved; // 0
  UINT desired_access; // 0xc0000000 (Generic Write | Generic Read)
  UINT file_attributes; // 0x00000080 (Normal)
  UINT share_access; // 0x00000003 (Read | Write)
  UINT create_disposition; // 0x00000001 (FILE_OPEN)
  UINT create_options; // 0
  USHORT name_offset;
  USHORT name_length;
  UINT create_contexts_offset; // ExtraInfo - Offset
  UINT create_contexts_length; // ExtraInfo - Length
  ANY buffer; // name + contexts
}
```

ほとんどNT_CREATE_ANDXの時と同じだけど、ExtraInfoだけがSMB1/CIFSの時に出てこなかったもの。これは、macosxでのパケットキャプチャ結果は、以下のような構造になってた。

```
CREATE_CONTEXT {
  UINT next; // 0
  USHORT name_offset; // 0x0010
  USHORT name_length; // 4
  USHORT reserved; // 0
  USHORT data_offset; // 0x0000
  UINT data_length; // 0
  ANY buffer; // "MxAc" + 0x00000000
}
```

MxAcという指定をしたいっぽいが、それが何かはわからない。調べてみたら、これはSMB2_CREATE_CONTEXT Request Valuesというものらしい。

[https://msdn.microsoft.com/en-us/library/cc246504.aspx](https://msdn.microsoft.com/en-us/library/cc246504.aspx)

説明として、「SMB2_CREATE_CONTEXT構造は、CREATE要求がどのように処理されなければならないかをリクエスト時に、CREATE要求が実際どのように処理されたかをレスポンス時にて、それぞれ追加のフラグや属性をエンコードするために、SMB2 CREATEリクエストおよびレスポンスによって使われます。」ということらしい。

このCONTEXTは複数指定することができるらしく、nextに次のCONTEXTの位置を指定することで実現可能。もしCONTEXTを1つだけ指定すれば良い場合には、0を指定しておく。そして、このnameやdata、8バイト境界で指定する必要がある。例えば今回のパケットキャプチャ結果の場合、dataは指定なしだけど、nameとして4文字指定すれば良いものを、8バイト境界とするために後ろに0x00を4つ付けて8バイト分のnameにしている。しかし、name_lengthには本当の長さである4を指定している。なんだかなぁ。。。

CREATEレスポンスについても、比較的項目は多い。その構造は以下。

```
CREATE_RESPONSE {
  USHORT structure_size;
  UCHAR oplock_level; // 0 (No lock)
  UCHAR flags; // 0 (Reserved)
  UINT create_action; // 1 (FILE_OPENED)
  ULONG creation_time; // SMB Timestamp
  ULONG last_access_time; // SMB Timestamp
  ULONG last_write_time; // SMB Timestamp
  ULONG change_time; // SMB Timestamp
  ULONG allocation_size;
  ULONG end_of_file;
  UINT file_attributes;
  UINT reserved; // 0
  UCHAR[16] file_id; // GUID
  UINT create_contexts_offset;
  UINT create_contexts_length;
  ANY buffer; // SMB2_CREATE_CONTEXT Response value
}
```

SMB2になって、FIDがGUIDになったらしい。他は特に問題なし。IPC$/srvsvcなので、タイムスタンプ系もサイズ系もみんな0だった。bufferには、リクエストで出てきたCONTEXTが指定されるっぽいけど、IPC$/srvsvcの結果としてはNO DATAだった。

IPC$/srvsvcのFIDが取れたら、いよいよDCE/RPCの出番。SMB2では、DCE/RPCの運搬ペイロードとして、SMB2 IOCTL（0x11）が使われる。DCE/RPCの作り方はきっとSMB1/CIFSと同じだろうから、とりあえずIOCTLの使い方だけ抑えれば大丈夫そう。IOCTLリクエストの構造は以下。

```
IOCTL_REQUEST {
  USHORT structure_size;
  USHORT reserved; // 0
  UINT ctl_code; // 0x0011C017 (FSCTL_PIPE_TRANSCEIVE)
  UCHAR[16] file_id;
  UINT input_offset; // SMB2ヘッダからの位置
  UINT input_count; // bufferの長さ
  UINT max_input_response; // 0
  UINT output_offset; // 0
  UINT output_count; // 0
  UINT max_output_response; // 65536
  UINT flags; // 0x00000001 (FSCTL:true)
  UINT reserved; // 0
  ANY buffer; // DCE/RPC
}
```

それほど難しくない。パケットキャプチャの結果、最初にBind(11)を送っているのも、SMB1/CIFSの時と同じだった。IOCTLレスポンスの構造は以下。

```
IOCTL_RESPONSE {
  USHORT structure_size;
  USHORT reserved; // 0
  UINT ctl_code; // 0x0011C017 (FSCTL_PIPE_TRANSCEIVE)
  UCHAR[16] file_id;
  UINT input_offset; // SMB2ヘッダからの位置
  UINT input_count; // 0
  UINT output_offset; // SMB2ヘッダからの位置
  UINT output_count; // bufferの長さ
  UINT flags; // 0
  UINT reserved; // 0
  ANY buffer; // DCE/RPC
}
```

注意点としては、リクエストの時はoutput_offsetが0だったのに、レスポンス時はinput_offsetとoutput_offsetの両方に値が入ってたこと。きっとinput_offsetが0でも大丈夫だとは思う。Bind(11)の結果としてBind_ack(12)が返ってくるところも、SMB1/CIFSと一緒だった。

その後共有リソース一覧を取得するのだが、同じくIOCTLを使って、DCE/RPCのNetShareEnumAllを送る。SMB1/CIFSと同じで良い。一つ後で確認すべきは、1回のレスポンスで受け取れなかった場合の挙動を把握しておかないと行けない。SMB1/CIFSの時は、OVERFLOWが返ってきて、その後SMB_COM_READ_ANDXを使って読み込めば良かったけど、SMB2ではどうなっているかを知っておく必要がある。あとでappserver1を動かして試してみる。

appserver1で試してみたけど、共有リソース20個ちょいでは、1回のレスポンスで返せてしまう模様。バッファサイズを小さく指定した上で試さないと、状況を作れないっぽい。macosxの場合は、max_output_countが65536に対して、appserver1が返してきたレスポンスは3668だったので、全然だめ。実際にコーディングするまで、この検証はお預けとする。

開いたものは閉じる。これが作法。まずはIPC$/srvsvcのクローズ処理。これはSMB2 CLOSE（6）を使う。そのリクエストの構造は以下。

```
CLOSE_REQUEST {
  USHORT structure_size;
  USHORT flags;
  UINT reserved; // 0
  UCHAR[16] file_id;
}
```

とても単純。正しく閉じれた後は、以下の構造のレスポンスが返ってくる。

```
CLOSE_RESPONSE {
  USHORT structure_size;
  USHORT flags; // 0
  UINT reserved; // 0
  ULONG creation_time; // SMB Timestamp
  ULONG last_access_time; // SMB Timestamp
  ULONG last_write_time; // SMB Timestamp
  ULONG change_time; // SMB Timestamp
  ULONG allocation_size;
  ULONG end_of_file;
  UINT file_attributes;
}
```

IPC$/srcsvcなので、ほとんどの値が0だった。次に、IPC$のクローズ処理。これにはSMB2 TREE_DISCONNECT（4）コマンドを利用する。TREE_CONNECTリクエストの構造は以下。

```
TREE_DISCONNECT_REQUEST {
  USHORT structure_size; // 4
  USHORT reserved; // 0
}
```

なんというバイトの無駄。将来何かフラグでも渡そうっていうことなのか。ヘッダにTree IDがあるので、上記は必要ない気がするが。TREE_DISCONNECTレスポンスも、実は上記と全く同じ構造だった。

```
TREE_DISCONNECT_RESPONSE {
  USHORT structure_size; // 4
  USHORT reserved; // 0
}
```

最後に、ログオフする。これには、SMB2 SESSION_LOGOFF（2）コマンドを利用する。SESSION_LOGOFFリクエストの構造は以下。さっきと全く一緒。

```
SESSION_LOGOFF_REQUEST {
  USHORT structure_size; // 4
  USHORT reserved; // 0
}
```

そして、レスポンスも一緒だった。

```
SESSION_LOGOFF_RESPONSE {
  USHORT structure_size; // 4
  USHORT reserved; // 0
}
```

これで、ネゴシエート、ユーザ認証、共有リソース一覧取得、クローズ、ログオフ、という基本の流れが把握できた。NTLMSSPやDCE/RPCの学習が既に終わっているところは非常に大きい。新しいことと言えば、ASN.1+DERだけど、これは実はmacosxからSMB1/CIFSで通信した時には適用されていたものだ。jCIFSではASN.1+DERは省略されていたので、僕の実装にもそれが入らなかった。SMB2の実装を書くときには、ASN.1+DERも適用した方がいいかもしれない。

SMB2、実装できる気がしてきた。
