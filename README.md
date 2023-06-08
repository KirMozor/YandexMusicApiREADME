<h1>YandexMusicApi</h1>

<img src="https://download.cdn.yandex.net/from/yandex.ru/support/ru/music/files/icon_main.png" width="7%">
<h3>Cross-platform Yandex Music API for C#</h3>
<h2>  
   <a href="https://www.nuget.org/packages/YandexMusicApi/">  
      <img src="https://img.shields.io/nuget/dt/YandexMusicApi?style=flat">  
   </a>
   <a href="https://gitlab.com/KirMozor/YandexMusicApi/-/blob/main/YandexMusicApi.csproj">  
      <img src="https://img.shields.io/badge/.NET-Standard%202.0-blueviolet?style=flat">  
   </a>
   <img src="https://badges.frapsoft.com/os/v1/open-source.svg?v=103?style=flat" >
   <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat">
   <a href="https://mastodon.social/@kirmozor">  
      <img src="https://img.shields.io/mastodon/follow/108763242400809137">  
   </a>
   <img src="https://img.shields.io/gitlab/stars/Xeronix/YandexMusicApi.svg?style=flat">
</h2>

## Description⚡

The YandexMusicApi library in C# allows for easy and convenient use of the Yandex.Music API to develop various music-related projects. It supports .NET Standard 2.0 and is available in a [NuGet package that has been downloaded over 4,000 times.](https://www.nuget.org/packages/YandexMusicApi/)


With this library, developers can access a wide range of Yandex.Music API functions, such as searching for songs, albums, artists, playlists, obtaining track information, obtaining links for streaming playback, and more. All API functions are provided in a user-friendly and easy-to-use form.

In addition, [this library is a fork of the original library written in Python by Marshal](https://github.com/MarshalX/yandex-music-api), which allows it to be used on C# for developing music projects.

The development of the library was based on the [website of Alexandr Cherkashin](https://github.com/acherkashin/yandex-music-open-api), where the Yandex.Music API is presented in the form of OpenAPI.


Overall, the YandexMusicApi library in C# is a powerful tool for developing applications and projects related to music on the Yandex.Music platform.


## How to install✨

Installation instructions for YandexMusicApi:

1. Install the .NET Core SDK on your computer if it is not already installed. You can download it from the official website: https://dotnet.microsoft.com/download

2. Open the terminal or command prompt on your computer.

3. Enter the following command in the terminal or command prompt:

    * dotnet add package YandexMusicApi

4. Wait for the installation to complete. After that, you can use the YandexMusicApi library in your C# project.

That's it! Now you can use the YandexMusicApi library to access the functions of the Yandex.Music API in your C# project.

## Example of use🍏

Let's look at examples of using the library😁

#### Receiving a token

All logic will be written in the comments, see there :). For now there is a two-factor authorization only by SMS
<pre>
<code>
public static async Task Main()
{
   NetworkParams networkParams = new NetworkParams() { }; // Creating an object of class NetworkParams, if necessary you can specify there proxy and UserAgent to use in requests.
   Token tokenObject = new Token("Login", "Password", networkParams);
   var result = await tokenObject.LoginUsername(); // Send Username to get authorization options

   if (result.Data["preferred_auth_method"].ToString() == "password") // If the best authorization option is a password
   {
       result = await tokenObject.LoginPassword(); // Starting authorization by password
       if (result.Data["errors"][0].ToString().Trim() == "redirect") // If you have two-factor authorization enabled
       {
           result = await tokenObject.CheckChallenge(); // Checking what Yandex needs for authorization
           if (result.Data["challenge"]["challengeType"].ToString().Trim() == "phone_confirmation") // If Yandex needs an SMS to confirm authorization
           {
               string phoneId = result.Data["challenge"]["phoneId"].ToString(); // Get phoneId to receive sms later
               result = await tokenObject.ValidatePhoneById(phoneId); // Check the received phoneId
               if (result.Data["status"].ToString().Trim() == "ok") // If all is well
               {
                   await tokenObject.PhoneConfirmCodeSubmit(phoneId); // Sending sms to phone

                   Console.WriteLine("Write SMS code");
                   string smsCode = Console.ReadLine();

                   await tokenObject.PhoneConfirmCode(smsCode); // Send the code to Yandex
                   result = await tokenObject.ChallengeCommit("phone_confirmation"); // Talking to Yandex about the end of two-factor authentication
                   var token = await tokenObject.GetToken(result.Data["retpath"].ToString()); // Getting a token

                   Console.WriteLine(token);
               }
           }
       }
   }
}
</code>
</pre>

#### Getting a direct link to the track

Get a direct link to a demo track of the top 3 tracks from the Evolve - Imagine Dragons album (30 second segment). All the logic of the work in the comments
<pre>
<code>
public static void Main()
{
   NetworkParams networkParams = new NetworkParams() { };
   Album albumApi = new Album(networkParams);
   Track trackApi = new Track(networkParams);

   var bestsTrack = albumApi.GetTracks(5568718).Result["result"]["bests"].ToList(); // Get the top 5 tracks from the Evolve - Imagine Dragons album
   foreach (var i in bestsTrack)
   {
       var downloadInfo = trackApi.GetDownloadInfo(i.ToString()).Result; // Get a list of available codecs and bitrates
       var downloadInfoUrl = downloadInfo["result"][0]["downloadInfoUrl"].ToString(); // Get the necessary string to get a direct link to the track
       var directLink = trackApi.GetDirectLink(downloadInfoUrl).Result; // Getting a direct link to the track (30 second demo)
       Console.WriteLine(directLink);
   }
}
</code>
</pre>

#### Album Search

An example with album search for your search query and parsing the API output. All logic in the comments

<pre>
<code>
public static void Main()
{
   NetworkParams networkParams = new NetworkParams() { };
   Default defaultApi = new Default(networkParams);

   Console.Write("Enter your search term: ");
   var searchRequest = Console.ReadLine();
   var searchResult = defaultApi.Search(searchRequest, typeSearch: "album").Result["result"]["albums"]["results"]; // Send a search query with a selection of albums from page 1 of the output and select from there the data for subsequent parsing
   foreach (var i in searchResult)
   {
       Console.WriteLine($"Title: {i["title"]}\nArtist: {i["artists"][0]["name"]}\nAlbum image link: {"https://" + i["coverUri"].ToString().Replace("%%", "100x100")}\n");
   }
}
</code>
</pre>

## Documentation

There is no online documentation for this project yet. But the documentation is in the code itself, you can look there😉

## Community

My channel on Telegram: https://t.me/kirmozorChannel

There is also a mirror in the Mastodon: https://mastodon.social/@kirmozor

We also have a chat room, the link is in the attached message of the channel😁

## Helping the channel and development

If you want to help this project as well as my channels, the details are below:
* Ton - UQAt_Dl_TAOCJVe09oXMS1XeMHz2DXX2JrCdA0cSyJaCRd1Q
* Btc - 1CNBs7r8NpwvZXanw5fx7h6ggrBwGzZVkS
* Tether (TRC20) - TDskFXZUFG3UCvAmaCxkdtyudWzYs5t7yf
* Tether (BEP20) - 0x51d5a4530670d81a2382edf266f4d5975e83d377
* BNB (BEP20) - 0x51d5a4530670d81a2382edf266f4d5975e83d377
* Visa - 4400430240807740
