# Flagstone

**Feature flags shouldn't be a black box, and they shouldn't cost more than your database.**

I'm building Flagstone because every feature flag tool I tried annoyed me in one of two ways. The hosted ones get expensive fast and hide what's actually happening when you flip a switch. The open-source ones are fine until the day your team needs SSO, and suddenly that's a paid tier. So this one does neither.

The basics work the way you'd expect: wrap code in a flag, roll it out to 10% of users, kill it in a second if something breaks. What's different is that every evaluation is traced with OpenTelemetry. When a rollout moves a metric, you open Grafana and actually see which users hit which rule and what it cost you in latency — instead of guessing and grepping logs at 3am.

And everything is in the open-source build. SSO, MFA, RBAC, audit log, change approvals — all of it, no paywall on security. Run it in your own VPC with Postgres and Redis and it's yours under MIT, no strings attached. If you'd rather not run it yourself, Flagstone Cloud is coming — and you'll pay us to operate it, never to unlock a feature.

## Try it in five minutes

```bash
git clone https://github.com/flagstonehq/flagstone
cd flagstone
docker compose up -d && docker compose --profile seed up seed
open http://localhost:3002   # login: admin@acme.com / password123
```

## Then in your code

```go
import "github.com/flagstonehq/flagstone/pkg/sdk"

client, _ := sdk.New(sdk.Options{
    Endpoint: "https://api.flagstone.dev",
    APIKey:   os.Getenv("FLAGSTONE_API_KEY"),
})
go client.Start(ctx)
defer client.Close()

if on, _ := client.Bool(ctx, "new-checkout", map[string]any{
    "user_id": 42, "country": "AR",
}); on {
    // new path
}
```

Flags evaluate locally, so this is a microsecond call, not a network round-trip. Changes you make in the dashboard reach the SDK over SSE in well under a second.

## The repos

- **[flagstone](https://github.com/flagstonehq/flagstone)** — the server, the dashboard, the rule engine, and the Go SDK.
- SDKs for other languages get their own repos as I get to them.

Go on the backend, Next.js for the dashboard, Postgres and Redis underneath.

## Where this is at

It's early. Flagstone is pre-1.0 and I'm building it in the open, so things move fast and some edges are still rough. If the idea resonates, star the repo or open an issue — early feedback is worth a lot right now, and I read all of it.
