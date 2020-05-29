# Variis Deconstruction
Reverse engineering the Variis app to view SoulCycle content on a Peloton.

# Abstract
Many indoor cycling riders have been faced with a choice between a Peloton bike and a SoulCycle-at-home bike.  Each is a unique product in their own way.  Peloton, a long-time leader in at-home cycling, has an established product that many either already own or have recently purchased.  SoulCycle, on the other hand, offers a new product (available in limited areas) that seems highly regarded for their classes and content, but has been difficult for many to gain access to.  This repository serves as data with regards to reverse engineering the SoulCycle-at-home app ("Variis") with the end goal of viewing such content on the Peloton's screen.

# Variis
The Variis app is SoulCycle (technically Equinox Media)'s iOS/Android app for viewing fitness content.  While not confirmed, it is believed that like Peloton, the SoulCycle-at-home bike runs the Android OS on its tablet, automatically launching the expected Variis app for best user experience.  The subsequent portion of this Readme is intended to provide research on reverse engineering the Variis app, with the end goal of reaching a direct URL to SoulCycle classes for viewing on other devices.  To note, it is NOT intended to "steal" content from SoulCycle/Equinox/Variis.  Upon completion of this project, **paying** users should be able to view SoulCycle content on their Peloton bikes.

# Variis Account
The Variis app requires a login (username and password).  At present, there are two known ways of acquiring a Variis login;
- Being an existing Equinox member, using your Equinox credentials to log into the Variis app
- By creating a Variis account during the purchase of the SoulCycle-at-home bike.
  - This can be performed by visting the purchase page for the SoulCycle-at-home bike (https://www.variis.com/bike/at-home) and entering a zip code where the bike is available (for example, 10014, the zip code of SoulCycle's corporate office).  Upon moving to the next step, choose *Reserve Your Bike* and enter credentials to create a Variis account.  Upon account creation, you can exist the browser.  No personal information/billing information needs to be entered.
  
# API Endpoints

# Login
  - Upon launching the Variis app on iOS/Android, the app will attempt to authenticate credentials.
  - The app will send the credentials to the following endpoint: `https://api.coreplatformx.com/authenticate/oauth/`
  - The data will be sent using the **POST** method with the following **JSON** payload;
  
  ```
  {
	"client_id": "app-mobile", // Unknown as to other "client_id" values
	"client_secret": "bV2hVMyXYvUFrRGvZWGWisjpRbxpsMsUEZ3UatFQkbhqX", // Randomly generated client_secret
	"deviceId": "5a8df095-7f51-4965-a69a-b13920020bd4", // Randomly generated device ID
	"grant_type": "password", // Unknown as to other "grant_type" values
	"password": "MyPassword", // The Variis account password
	"username": "email@email.com" // The Variis account e-mail
}
```

  - A response will return the following values;
  ```
  {
	"errors": [{
		"message": "Error attempting passport authentication",
		"code": "01203"
	}]
}
// Indicates a valid e-mail but incorrect password.
```

```
{
	"errors": [{
		"message": "Unable to authenticate user with provided credentials",
		"code": "01203"
	}]
}
// Indicates an invalid e-mail/username not found in system.
```

```
{
	"data": {
		"access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9eyJ1c2VySWQiOiJmYzJjZjcyYi00ZjFlLTQxZDItYTFmMy0wMGU2NDJjYTE5NjkiLCJkZXZpY2VJZCI6IjVhOGRmMDk1LTdmNTEtNDk2NS1hNjlhLWIxMzkyMDAyMGJkNCIsImJyYW5kcyI6e30sImNsaWVudElkIjoiYXBwLW1vYmlsZSIsInJlZnJlc2hJZCI6IjFiMzU2MWUxLTE5M2EtNGQ4NS1hMzk2LTNkNTIwOWQ5ZWY5ZSIsInN1YnNjcmlwdGlvblR5cGUiOm51bGwsInN1YnNjcmlwdGlvblNvdXJjZSI6bnVsbCwic3Vic2NyaXB0aW9uQWN0aXZlIjpmYWxzZSwiaWF0IjoxNTkwNzEwOTQ1LCJleHAiOjE1OTA3MTQ1NDV9w7c+w6DCs33Dk8Ksw6E9w4fDj1h7LhkPwrHChcKNwpLDs8KEN0TCtGEdw4/CgmHChR0=", // Base64 encoded string with account data.  See below.
		"expires_in": 3599, // Time the access valid is valid for before a new token is required
		"token_type": "bearer", // Unknown other values for "token_values"
		"refresh_token": "0ff4a04e-0811-4c04-b1fe-da86cd194819" // Unknown use
	}
}
// Valid reponse
```

The `access_token` can be decoded, which indicates the following data;
```
{
 "alg":"HS256",
 "typ":"JWT"
}
{
 "userId":"fc2cf72b-4f1e-41d2-a1f3-00e642ca1969",
 "deviceId":"5a8df095-7f51-4965-a69a-b13920020bd4",
 "brands":{},
 "clientId":"app-mobile",
 "refreshId":"1b3561e1-193a-4d85-a396-3d5209d9ef9e",
 "subscriptionType":null,
 "subscriptionSource":null,
 "subscriptionActive":false,
 "iat":1590710000,
 "exp":1590713600
}
```



# Peloton
The Peloton "tablet" runs a variant of Android OS; Peloton itself is an app on the Android device, and like any Android smartphone, there is a home screen and built-in browser.  Multiple methods of accessing the built-in browser exist (two are linked below).  Upon completion of the above "to-do," it is believed that accessing a dedicated website, which leverages many of the aforementioned APIs, could be created to allow users subscribed to Variis to view classes on their Peloton bike.
- https://www.theverge.com/2019/8/14/20801983/youtube-plex-web-browser-peloton-bike-treadmill-how-to-watch
- https://www.reddit.com/r/pelotoncycle/comments/a7ttuz/browser_without_subscription/
