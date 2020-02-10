# Style Guide

### Branching model

Branching model is described in [Git Branching model][git-branching].

### Versioning conventions

1. If project is in monorepo (`snowplow/snowplow`, `snowplow/snowplow-rdb-loader` etc), i.e. containing multiple projects that can be used separately, we used to refer to its milestones as to *releases* (e.g. `Release 32` or `R32`). We're trying to move away from monorepos
2. If project is not a monorpo (`snowplow/snowplow-scala-tracker`, `snowplow/dataflow-runner` etc) containing only one project, we used to refer to its milestones as to *versions* (e.g. `Version 0.5.0`)
3. If project is a library (`snowplow/snowplow-scala-tracker`, `snowplow/snowplow-scala-analytics-sdk` etc) and cannot be used on its own, we used to refer to its pre-release assets as to *milestones* (e.g. `0.5.0-M2`)
4. If project is an application (`snowplow/snowplow-rdb-loader`, `snowplow/dataflow-runner` etc) and has an executable, we used to refer to its pre-release assets as to *release candidates* (e.g. `0.5.0-rc2`)
5. The library versioning convention is primarly used for Scala libraries, but generally should be applicable to any language. In case there's a strong convention in a language community to use other suffixes (e.g. `alpha`, `rc`) - the community convention should have a priority

Above conventions affect CI/CD process, if you need to find out how a project from monorepo gets deployed - you need to look at another monorepo.

### Code hygiene

* Every bugfix (ticket labelled with `bug`) requires at least one regression test

## Scala

All Scala libraries and applications should follow practices outlined in our [Scala App template][scala-app-template]

### Random set of recomendations

* Don't use `App`/`Application` traits. Only `IOApp` if necessary
* Add `()` to side-effecting methods definitions and calls
* Prefer fully-qualified imports instead of omitting head of import (and thus no package-nesting)
* File `SomeThing.scala` can contain only `SomeThing` trait/class and its companion object
* File `something.scala` can contain any number of entities with any names, but generally discouraged

[scala-app-template]: https://github.com/snowplow/snowplow-scala-app.g8
[scala-style-guide]: https://docs.scala-lang.org/style/
[git-branching]: ./git-branching.md
