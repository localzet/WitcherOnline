# Combined co-op review build

This fork-only branch combines WitcherOnline PRs #2–#6 against the 0.6.1 base.
It is not an additional upstream PR.

## Java server (JDK 21)

From the repository root:

```sh
mkdir -p server/build/review
javac -encoding UTF-8 -Xlint:all -d server/build/review server/src/*.java server/tests/*.java
java -cp server/build/review SessionTimeoutTest
java -cp server/build/review CoopOrderingTest
python3 server/tests/udp_review_test.py server/build/review
```

## Game checks

Use the supported game build and W3Coop test harness, with identical saves
on the base and review versions.

- Deliver enemy snapshots in order 10, 12, 11. The last packet must not
  change position/health or release puppets. Repeat 12; restart the leader.
- Repeat a loot list before and after a kill; expect one fill per epoch.
  Reuse the enemy ID in a new epoch; do not apply an old epoch's loot.
- Reconnect a member with a new session, then deliver a retired session's
  packets. Expect no repeated hits or hurt acknowledgements.
- Run existing shared-enemy, damage, loot, journal and cutscene scenarios.
- Check two guests, one disconnecting and returning while the other stays.

Game-side checks require the native WitcherScript compiler and game.
Passing Java tests does not establish in-game correctness.
