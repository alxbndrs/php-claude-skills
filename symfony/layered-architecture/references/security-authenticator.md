# Security -- Authenticator

```php
final class UsernamePasswordAuthenticator extends AbstractAuthenticator
{
    public function supports(Request $request): ?bool
    {
        return $request->getPathInfo() === '/api/login' && $request->isMethod('POST');
    }

    public function authenticate(Request $request): Passport
    {
        $data = json_decode($request->getContent(), true);

        return new Passport(
            new UserBadge($data['username']),
            new PasswordCredentials($data['password']),
        );
    }

    public function onAuthenticationSuccess(Request $request, TokenInterface $token, string $firewallName): ?Response
    { /* return JWT response */ }

    public function onAuthenticationFailure(Request $request, AuthenticationException $exception): ?Response
    { /* return error response */ }
}
```
