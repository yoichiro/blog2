---
layout: post
status: publish
published: true
title: OpenSocialで規定された会員情報の多さ
author:
  display_name: yoichiro
  login: yoichiro
  email: yoichiro@eisbahn.jp
  url: ''
author_login: yoichiro
author_email: yoichiro@eisbahn.jp
wordpress_id: 621
wordpress_url: http://www.eisbahn.jp/yoichiro/?p=621
date: '2009-02-01 00:10:52 +0900'
date_gmt: '2009-01-31 15:10:52 +0900'
categories:
- OpenSocial
---

OpenSocialでは、SNSが持つ会員の情報を扱うために、予め情報の項目が規定されています。その項目群は、いくつかの主要SNSを調査した結果から最大公約数的に選ばれた項目によって定義されています。その項目数は、かなりの数となります。

その多さは、以下のコードを見れば実感することができるでしょう。このコードは、ある一人の会員情報をJavaにて構築する例です。このコードは、
[OSDE](http://www.eisbahn.jp/trac/osde)内で利用されています。

```
private Person createCanonicalPerson() {
Person canonical = new PersonImpl();
canonical.setId("canonical");
canonical.setAboutMe("I have an example of every piece of data");
canonical.setActivities(Arrays.asList("Coding Shindig"));
Address address = new AddressImpl();
address.setCountry("US");
address.setLatitude(28.3043f);
address.setLongitude(143.0859f);
address.setLocality("who knows");
address.setPostalCode("12345");
address.setRegion("Apache, CA");
address.setStreetAddress("1 OpenStandards Way");
address.setType("home");
address.setFormatted("PoBox 3565, 1 OpenStandards Way, Apache CA");
canonical.setAddresses(Arrays.asList(address));
canonical.setAge(33);
BodyType bodyType = new BodyTypeImpl();
bodyType.setBuild("svelte");
bodyType.setEyeColor("blue");
bodyType.setHairColor("black");
bodyType.setHeight(1.84f);
bodyType.setWeight(74f);
canonical.setBodyType(bodyType);
canonical.setBooks(Arrays.asList("The Cathedral & the Bazaar", "Catch 22"));
canonical.setCars(Arrays.asList("beetle", "prius"));
canonical.setChildren("3");
Address currentLocation = new AddressImpl();
currentLocation.setLatitude(48.858193f);
currentLocation.setLongitude(2.29419f);
canonical.setCurrentLocation(currentLocation);
canonical.setBirthday(Gadgets.getDate(1975, 0, 1));
canonical.setDisplayName("Shin Digg");
canonical.setDrinker(new DrinkerImpl(Drinker.SOCIALLY, "Socially"));
ListField email = new EmailImpl();
email.setValue("shindig-dev@incubator.apache.org");
email.setType("work");
canonical.setEmails(Arrays.asList(email));
canonical.setEthnicity("developer");
canonical.setFashion("t-shirts");
canonical.setFood(Arrays.asList("sushi", "burgers"));
canonical.setGender(Gender.male);
canonical.setHappiestWhen("coding");
canonical.setHasApp(true);
canonical.setHeroes(Arrays.asList("Doug Crockford", "Charles Babbage"));
canonical.setHumor("none to speak of");
canonical.setInterests(Arrays.asList("PHP", "Java"));
canonical.setJobInterests("will work for beer");
Organization organization1 = new OrganizationImpl();
address = new AddressImpl();
address.setFormatted("1 Shindig Drive");
organization1.setAddress(address);
organization1.setDescription("lots of coding");
organization1.setEndDate(Gadgets.getDate(2010, 9, 10));
organization1.setField("Software Engineering");
organization1.setName("Apache.com");
organization1.setSalary("$1000000000");
organization1.setStartDate(Gadgets.getDate(1995, 0, 1));
organization1.setSubField("Development");
organization1.setTitle("Grand PooBah");
organization1.setWebpage("http://incubator.apache.org/projects/shindig.html");
organization1.setType("job");
Organization organization2 = new OrganizationImpl();
address = new AddressImpl();
address.setFormatted("1 Skid Row");
organization2.setAddress(address);
organization2.setDescription("");
organization2.setEndDate(Gadgets.getDate(1995, 0, 1));
organization2.setField("College");
organization2.setName("School of hard knocks");
organization2.setSalary("$100");
organization2.setStartDate(Gadgets.getDate(1991, 0, 1));
organization2.setSubField("Lab Tech");
organization2.setTitle("Gopher");
organization2.setWebpage("");
organization2.setType("job");
canonical.setOrganizations(Arrays.asList(organization1, organization2));
canonical.setLanguagesSpoken(Arrays.asList("English", "Dutch", "Esperanto"));
canonical.setUpdated(Gadgets.getDate(2006, 5, 6, 12, 12, 12));
canonical.setLivingArrangement("in a house");
Enum
lookingFor1 = new LookingForImpl();
lookingFor1.setValue(LookingFor.RANDOM);
lookingFor1.setDisplayValue("Random");
Enum
lookingFor2 = new LookingForImpl();
lookingFor2.setValue(LookingFor.NETWORKING);
lookingFor2.setDisplayValue("Networking");
canonical.setLookingFor(Arrays.asList(lookingFor1, lookingFor2));
canonical.setMovies(Arrays.asList("Iron Man", "Nosferatu"));
canonical.setMusic(Arrays.asList("Chieftains", "Beck"));
Name name = new NameImpl();
name.setAdditionalName("H");
name.setFamilyName("Digg");
name.setGivenName("Shin");
name.setHonorificPrefix("Sir");
name.setHonorificSuffix("Social Butterfly");
name.setUnstructured("Sir Shin H. Digg Social Butterfly");
canonical.setName(name);
canonical.setNetworkPresence(new NetworkPresenceImpl(NetworkPresence.ONLINE, "Online"));
canonical.setNickname("diggy");
canonical.setPets("dog,cat");
PhoneNumberImpl phone1 = new PhoneNumberImpl();
phone1.setValue("111-111-111");
phone1.setType("work");
PhoneNumberImpl phone2 = new PhoneNumberImpl();
phone2.setValue("999-999-999");
phone2.setType("mobile");
canonical.setPhoneNumbers(Arrays.asList((ListField)phone1, (ListField)phone2));
canonical.setPoliticalViews("open leaning");
UrlImpl profileSong = new UrlImpl();
profileSong.setValue("http://www.example.org/songs/OnlyTheLonely.mp3");
profileSong.setLinkText("Feelin' blue");
profileSong.setType("road");
canonical.setProfileSong(profileSong);
canonical.setProfileUrl("http://www.example.org/?id=1");
UrlImpl profileVideo = new UrlImpl();
profileVideo.setValue("http://www.example.org/videos/Thriller.flv");
profileVideo.setLinkText("Thriller");
profileVideo.setType("video");
canonical.setProfileVideo(profileVideo);
canonical.setQuotes(Arrays.asList("I am therfore I code", "Doh!"));
canonical.setRelationshipStatus("married to my job");
canonical.setReligion("druidic");
canonical.setRomance("twice a year");
canonical.setScaredOf("COBOL");
canonical.setSexualOrientation("north");
canonical.setSmoker(new SmokerImpl(Smoker.NO, "No"));
canonical.setSports(Arrays.asList("frisbee", "rugby"));
canonical.setStatus("happy");
canonical.setTags(Arrays.asList("C#", "JSON", "template"));
canonical.setThumbnailUrl("http://www.example.org/pic/?id=1");
canonical.setUtcOffset(-8l);
canonical.setTurnOffs(Arrays.asList("lack of unit tests", "cabbage"));
canonical.setTurnOns(Arrays.asList("well document code"));
canonical.setTvShows(Arrays.asList("House", "Battlestart Galactica"));
UrlImpl url1 = new UrlImpl();
url1.setValue("http://www.example.org/?id=1");
url1.setLinkText("my profile");
url1.setType("profile");
UrlImpl url2 = new UrlImpl();
url2.setValue("http://www.example.org/pic/?id=1");
url2.setLinkText("my awesome picture");
url2.setType("thumbnail");
canonical.setUrls(Arrays.asList((Url)url1, (Url)url2));
return canonical;
}
```

もちろん、この全ての項目をサポートするSNSは現実的にはありません。どの項目をサポートするかは、個々のSNSのポリシーに依存します。
