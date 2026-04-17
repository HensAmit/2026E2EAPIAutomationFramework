# Spotify API Test Framework

Production-grade Cucumber + RestAssured API test framework for Spotify APIs.

## Tech Stack

| Concern | Library |
|---|---|
| Test definition | Cucumber 7 (BDD feature files) |
| HTTP client | RestAssured 5 |
| Runner | JUnit 5 Platform Suite |
| Dependency Injection | PicoContainer (scenario-scoped) |
| JSON payload manipulation | Jayway JsonPath (DocumentContext) |
| Assertions | AssertJ (soft assertions) |
| Configuration | OWNER library |
| Async polling | Awaitility |
| Test data generation | Java Faker |
| Reporting | ExtentReports 5 |
| Logging | Log4j2 |

## Project Structure

```
src/
├── main/java/com/spotify/framework/
│   ├── auth/          AuthManager, TokenCache, SpotifyTokenFetcher
│   ├── config/        AppConfig (OWNER interface), ConfigManager
│   ├── context/       TestContext (PicoContainer scenario-scoped state)
│   ├── service/       ApiService, PayloadService, AssertionService,
│   │                  PlaceholderResolver, ValueParser, TestDataFactory
│   ├── utils/         WaitUtils, RandomDataUtils
│   └── report/        ExtentManager, ExtentTestManager
│
└── test/
    ├── java/com/spotify/tests/
    │   ├── runner/    TestRunner
    │   ├── steps/     BaseSteps, CommonSteps, PlaylistSteps
    │   └── hooks/     Hooks
    └── resources/
        ├── features/playlist/   *.feature files
        ├── payloads/playlist/   JSON payload templates
        ├── schemas/             JSON Schema files
        ├── config/              dev.properties, staging.properties
        ├── junit-platform.properties
        └── log4j2.xml
```

## Prerequisites

- Java 17+
- Maven 3.9+
- A Spotify Developer account with an app created at https://developer.spotify.com/dashboard

## Spotify API Setup

1. Create an app at https://developer.spotify.com/dashboard
2. Note your **Client ID** and **Client Secret**
3. Add `http://localhost:8080/callback` as a Redirect URI in the app settings
4. Obtain a refresh token with the required scopes (see `dev.properties` for scope list)
5. Export credentials as environment variables:

```bash
export SPOTIFY_CLIENT_ID=your_client_id
export SPOTIFY_CLIENT_SECRET=your_client_secret
export SPOTIFY_REFRESH_TOKEN=your_refresh_token
export SPOTIFY_USER_ID=your_spotify_user_id
```

## Running Tests

```bash
# Run all tests locally (dev environment)
mvn test

# Run with specific tags
mvn test -Dtags="@smoke"
mvn test -Dtags="@createPlaylist"
mvn test -Dtags="@regression"

# Run against staging
mvn test -Denv=staging -Dtags="@smoke"

# Run with CI profile (parallel, staging env)
mvn test -Pci -Dtags="@regression"

# Run failed tests only (after a previous run generated target/rerun.txt)
mvn test -Dcucumber.features="@target/rerun.txt"
```

## Build Parts

The framework is built incrementally across 14 parts:

| Part | Contents |
|------|----------|
| 1 | pom.xml + project skeleton (this part) |
| 2 | AppConfig + .properties files |
| 3 | AuthManager + TokenCache + SpotifyTokenFetcher |
| 4 | TestContext |
| 5 | PayloadService + PlaceholderResolver + ValueParser |
| 6 | ApiService + RequestResponseFilter |
| 7 | AssertionService |
| 8 | Hooks + ExtentReports |
| 9 | WaitUtils + RandomDataUtils + TestDataFactory |
| 10 | BaseSteps + CommonSteps + TestRunner |
| 11 | PlaylistSteps + Create Playlist feature |
| 12 | Get / Update / Delete playlist flows |
| 13 | Negative tests + boundary cases |
| 14 | Dockerfile + Jenkinsfile + docker-compose |

## Reports

After a test run:
- **Extent HTML report**: `target/reports/extent-report.html`
- **Cucumber HTML report**: `target/reports/cucumber-report.html`
- **Surefire XML** (Jenkins trend graphs): `target/surefire-reports/`
- **Logs**: `target/logs/framework.log`
