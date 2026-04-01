# Infrastructure Client

Generic, domain-agnostic client pattern. No business logic -- only API communication.

```
Infrastructure/
└── Facebook/
    ├── FacebookClientInterface.php
    ├── FacebookClient.php
    └── Model/
        ├── FacebookUser.php
        └── FacebookToken.php
```

```php
interface FacebookClientInterface
{
    public function getUser(string $accessToken): FacebookUser;
}
```

```php
final readonly class FacebookClient implements FacebookClientInterface
{
    public function __construct(
        private HttpClientInterface $httpClient,
        private string $appId,
        private string $appSecret,
    ) {}

    public function getUser(string $accessToken): FacebookUser
    {
        $response = $this->httpClient->request('GET', 'https://graph.facebook.com/me', [
            'query' => [
                'access_token' => $accessToken,
                'fields' => 'id,name,email',
            ],
        ]);

        $data = $response->toArray();

        return new FacebookUser($data['id'], $data['name'], $data['email'] ?? null);
    }
}
```
