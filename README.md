# Meelz

Mobile app made in Flutter

#### Table of Contents

1. [Create account screen](#create-account-screen)
2. [Confirm number screen](#confirm-number-screen)

## Create account screen

App bar used on this screen is reusable widget and it can be called like:
```dart
    ReusableWidgets.meelzAppBarWithSkip(context, function)
```  
where context is _BuildContext_ of _Scaffold_ and function is function which we want to be triggered on tap of skip button in this appbar.\
On this screen skip button leads to _Home page_.\
All reusable widgets can be found in _lib/shared_ and _lib/shared/reusableWidgets.dart_.

For creating account we need to add phone number.\
For inputing phone number we added package:
```r
    international_phone_input: ^1.0.4
```
which provides us with all available international phone number codes and validators for all of them.
We can enable this input to have only some international codes with this snippet of code:
```dart
    //add more enabled countries here
    enabledCountries: [
        '+387',
        '+971',
    ]
```  

We can also set inital code and if we want this input to show flags with code:
```dart
    initialSelection: "+971",
    showCountryFlags: true
```

__Next__ button will be enabled if verification of __InternationalPhoneInput__ widget is valid.

When we click __Next__ button on this screen we firstly send __POST__ request to check if phone exists for another user with this snippet of code:
```dart
    const String url = "https://devapi.meelz.me/auth/check-phone";
    http.Response response = await http.post(
        url,
        headers: {"Content-Type": "application/json"},
        body: jsonEncode({
          "phone": Provider.of<PhoneNumberInputNotifier>(context, listen: false)
              .phoneNumber
        }),
    );
```
If we get response with _status code_ of __200__ it means that there is no this phone in database.\
Other status codes tell us that there is this phone in database.

After checking does the phone exists we send code to phone with next snippet of code:
```dart
    const String url2 = "https://devapi.meelz.me/auth/send-phone";
        http.Response response2 = await http.post(
          url2,
          headers: {"Content-Type": "application/json"},
          body: jsonEncode({
            "phone":
                Provider.of<PhoneNumberInputNotifier>(context, listen: false)
                    .phoneNumber
          }),
        );
```
If we get _status code_ __201__ it means that code has been sent.\
If we get other _status codes_ it means there is some problem.

After sending code we go to __Confirmation number screen__. 

On __Create account screen__ beneath __InternationalPhoneInput__ we also have _Facebook_, _Google_ and on _Apple_ devices _Apple_ login.

## Confirm number screen

App bar used on this screen is reusable widget and it can be called like:
```dart
    appBarWithBackButton(context, title)
```  
where context is _BuildContext_ of _Scaffold_ and title is title which we want to be in middle of app bar.\
This app bar is also reusable from _lib/shared_ folder.

Main function of this screen is to input code which has been sent on previous screen.\
On center of screen we have input fields for code.\
When we click __Next__ button on this screen we send __POST__ request with code inputed in textfields:
```dart
    String url = "https://devapi.meelz.me/auth/check-code/$code";
    http.Response response = await http.post(
        url,
        headers: {"Content-Type": "application/json"},
    );
```
If we get response with _status code_ of __201__ it means that code is _OK_.\
Other status codes tell us that code is _NOT OK_.

We also have _Try again_ button which sends code again.
