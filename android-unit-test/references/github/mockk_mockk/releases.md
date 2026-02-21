# Releases: mockk/mockk

## 1.14.7: v1.14.7

**Published**: 2025-12-06

## What's Changed
* fix: normalize value class arguments in EqMatcher for consistent comparison by @edwardmp in https://github.com/mockk/mockk/pull/1440
* Add configurable logging to withArg & withNullableArg by @OsaSoft in https://github.com/mockk/mockk/pull/1441
* docs(readme): document suppressing superclass calls by @ch200203 in https://github.com/mockk/mockk/pull/1444
* Fix for issue #1103. by @sdetilly in https://github.com/mockk/mockk/pull/1449
* Fix configuration option example for 

## 1.14.6: 1.14.6

**Published**: 2025-10-01

## What's Changed
* Fix and add clearMocks test cases by @jmatsu in https://github.com/mockk/mockk/pull/1419
* make captured value reference volatile by @mmimica in https://github.com/mockk/mockk/pull/1418
* add fail-fast guard for Kotlin inline function mocking (#1030) by @ch200203 in https://github.com/mockk/mockk/pull/1421
* add test reports to GitHub actions by @aSemy in https://github.com/mockk/mockk/pull/994
* fix duration denormalized error by @tigermint in https://github.com/mockk/m

## 1.14.5: 1.14.5

**Published**: 2025-07-16

## What's Changed
* [Feature] Implement BDD-style aliases as separate module (mockk-bdd) by @Minseok-2001 in https://github.com/mockk/mockk/pull/1399
* fix: downgrade byte-buddy to 1.5.11 by @Komdosh in https://github.com/mockk/mockk/pull/1413

## New Contributors
* @Minseok-2001 made their first contribution in https://github.com/mockk/mockk/pull/1399

**Full Changelog**: https://github.com/mockk/mockk/compare/1.14.4...1.14.5

## 1.14.4: 1.14.4

**Published**: 2025-06-22

This release is functionally equivalent to v1.14.3, I just wanted to try out the new publishing process that uses Maven Central instead of OSSRH.

**Full Changelog**: https://github.com/mockk/mockk/compare/1.14.3...1.14.4

## 1.14.3: 1.14.3

**Published**: 2025-06-21

## What's Changed
* chore: bump byte buddy version to support java 24 by @Komdosh in https://github.com/mockk/mockk/pull/1387
* chore: bump gradle version to 8.14 by @Komdosh in https://github.com/mockk/mockk/pull/1389
* chore: bump dokka, kotlinx.coroutines, slf4j, logback, junit jupiter by @Komdosh in https://github.com/mockk/mockk/pull/1388
* Add recursive detection of meta-annotations by @VitalyVPinchuk in https://github.com/mockk/mockk/pull/1393
* chore: bump kotlin to 2.1.20 by @Komdo

## 1.14.2: 1.14.2

**Published**: 2025-04-29

## What's Changed
* Change mockk configuration paths to test sourceSet by @TWiStErRob in https://github.com/mockk/mockk/pull/1378
* Use redefine if mockkStatic is used (fix candidate for #1375) by @sgerke-1L in https://github.com/mockk/mockk/pull/1376


**Full Changelog**: https://github.com/mockk/mockk/compare/1.14.0...1.14.2

## 1.14.0: 1.14.0

**Published**: 2025-04-09

## What's Changed
* Update the kdoc of verifyAll to describe its behavior correctly by @harel-biton in https://github.com/mockk/mockk/pull/1363
* Fix returning result values by @burnoo in https://github.com/mockk/mockk/pull/1352
* fix: ensure confirmVerified is isolated per test by @rsvinicius in https://github.com/mockk/mockk/pull/1367
* Do not generate visibility bridge for package-private default methods (fixes #1118) by @sgerke-1L in https://github.com/mockk/mockk/pull/1366
* fix: handl

## 1.13.17: 1.13.17

**Published**: 2025-03-04

## What's Changed
* Fix(Issue #1333): Bug fix for the issue with MockK 1.13.16 Wraps Results Objects Twice by @kpadhiamex in https://github.com/mockk/mockk/pull/1334
* Fix (issue# 1329) parallel testing for unmockkAll by @kpadhiamex in https://github.com/mockk/mockk/pull/1335
* [#1304] feat: Restrict mocking of certain classes and add configuration option  by @devtaebong in https://github.com/mockk/mockk/pull/1340
* Update README.md - Clarify that private fields cannot be mocked by @p4ulor i

## 1.13.16: 1.13.16

**Published**: 2025-01-10

## What's Changed
* Fix( Issue #1073): Bug fix for the issue with mocking value classes with coEvery by @kpadhiamex in https://github.com/mockk/mockk/pull/1332


**Full Changelog**: https://github.com/mockk/mockk/compare/1.13.14...1.13.16

## 1.13.14: 1.13.14

**Published**: 2024-12-20

## What's Changed
* fix(1308): Handle nullable complex and nested value classes by @VasilisDrettas-tomtom in https://github.com/mockk/mockk/pull/1314
* Fix(Issue no. 1330) for Relaxed Mocking Value When Property is Nested Value Class by @kpadhiamex in https://github.com/mockk/mockk/pull/1331

## New Contributors
* @kpadhiamex made their first contribution in https://github.com/mockk/mockk/pull/1331

**Full Changelog**: https://github.com/mockk/mockk/compare/1.13.13...1.13.14

