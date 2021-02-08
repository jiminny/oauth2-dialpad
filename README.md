# Dialpad Provider for OAuth 2.0 Client

This package provides Dialpad OAuth 2.0 support for the PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).

## Installation

To install, use composer:

```
composer require jiminny/oauth2-dialpad
```

## Usage

Usage is the same as The League's OAuth client, using `\Jiminny\OAuth2\Client\Provider\Dialpad` as the provider.

### Authorization Code Flow

```php
$provider = new Jiminny\OAuth2\Client\Provider\Dialpad([
    'clientId'          => '{dialpad-client-id}',
    'clientSecret'      => '{dialpad-client-secret}',
    'redirectUri'       => 'https://example.com/callback-url',
    'host'              => 'https://dialpad.com' // Defaults to https://dialpad.com
    'isSandbox'         => true, // Optional, defaults to false. When true, client uses sandbox urls.
]);

if (!isset($_GET['code'])) {

    // If we don't have an authorization code then get one
    $authUrl = $provider->getAuthorizationUrl();
    $_SESSION['oauth2state'] = $provider->getState();
    header('Location: '.$authUrl);
    exit;

// Check given state against previously stored one to mitigate CSRF attack
} elseif (empty($_GET['state']) || ($_GET['state'] !== $_SESSION['oauth2state'])) {

    unset($_SESSION['oauth2state']);
    exit('Invalid state');

} else {

    // Try to get an access token (using the authorization code grant)
    $token = $provider->getAccessToken('authorization_code', [
        'code' => $_GET['code']
    ]);

    // Optional: Now you have a token you can look up a users profile data
    try {

        // We got an access token, let's now get the user's details
        $user = $provider->getResourceOwner($token);

        // Use these details to create a new profile
        printf('Hello %s!', $user->getId());

    } catch (Exception $e) {

        // Failed to get user details
        exit('Oh dear...');
    }

    // Use this to interact with an API on the users behalf
    echo $token->getToken();
}
```

### Refreshing a Token

The Dialpad API supports refresh tokens. Review the "[Refreshing a Token documentation](https://github.com/thephpleague/oauth2-client#refreshing-a-token)" on the base `oauth2-client` project for tips on implementing refresh tokens in your project.

## Testing

``` bash
$ ./vendor/bin/phpunit
```

## Contributing

Please see [CONTRIBUTING](https://github.com/jiminny/oauth2-dialpad/blob/master/CONTRIBUTING.md) for details.


## Credits

- [James Graham](https://github.com/jiminny)
- [All Contributors](https://github.com/jiminny/oauth2-dialpad/contributors)


## License

The MIT License (MIT). Please see [License File](https://github.com/jiminny/oauth2-dialpad/blob/master/LICENSE) for more information.
