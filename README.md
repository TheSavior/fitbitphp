## FitbitPHP ##

Basic wrapper for OAuth-based [FitBit](http://fitbit.com) [REST API](http://dev.fitbit.com). Seek more information on API developments at [dev.fitbit.com](http://dev.fitbit.com). This library does **NOT** require the OAuth extension to be installed in PHP. This should work on any server with php >= 5.4.

Library is in BETA as well as the API, so it could still be buggy. We're looking forward to update the library as API moves forward, doing best not to break backward compatibility. That being said, feel free to fork, add features and send pull request to us if you need more awesomness right now, we'll be happy to include them if well done.

**Current notes:**

 * *Subscriptions*: Library has basic methods to add/delete subscriptions, unfortunately it's your headache to track the list and deploy server endpoints to receive notifications from Fitbit as well as register them at [http://dev.fitbit.com](http://dev.fitbit.com). See [Subscriptions-API](http://wiki.fitbit.com/display/API/Subscriptions-API) for more thoughts on that,
 * *Unauthenticated calls*: Some methods of Fitbit API grant access to public resources without need for the complete OAuth workflow, `searchFoods` and `getActivities` are two good example of such endpoints. Nevertheless, this calls should be signed with Authentication header as usual, but access_token parameter is omitted from signature base string. In terms of FitbitPHP, you can make such calls, but you shouldn't use `initSession` (so access_token wouldn't be set) and should explicitly set the user to fetch resources from before the call (via `setUser`).

## Installation ##
This package is installable with composer:
    "thesavior/fitbit": "dev-master"

## Usage ##

First, as always don't forget to register your application at http://dev.fitbit.com and obtain consumer key and secret for your application.

Library itself handles whole OAuth application authorization workflow for you as well as session tracking between page views. This could be used further to provide 'Sign with Fitbit' like feature (look at next code sample) or just to authorize application to act with FitBit API on user's behalf.

Example snippet on frontend could look like:

    <?php
    $fitbit = new \Fitbit\Api($_SERVER["clientid", "clientsecret"]);

    $fitbit->initSession();
    $json = $fitbit->getProfile();

    print_r($json);

Note, that unconditional call to 'initSession' in each page will completely hide them from the eyes of unauthorized visitor. Don't be amazed, however, it's not a right way to make area private on your site. On the other hand, you could just track if user already authorized access to FitBit without any additional workflow, if it was not true:

    if($fitbit->isAuthorized())
        <you_are_authorized_user_yes_you_are>


**Note.** By default, all requests are made to work with resources of authorized user (viewer), however you can use `setUser` method to set another user, this would work only for several endpoints, which grant access to resources of other users and only if that user granted permissions to access his data ("Friends" or "Anyone").

If you want to fetch data without complete OAuth workflow, only using consumer_key without access_token, you can do that also (check which endpoints are okey with such calls on Fitbit API documentation):

    require 'fitbitphp.php';

    $fitbit = new \Fitbit\Api(FITBIT_KEY, FITBIT_SECRET);

    $fitbit->setUser('XXXXXX');
    $json = $fitbit->getProfile();

    print_r($json);
