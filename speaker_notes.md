# Dancing with Elephants - Gradle Tips & Tricks

## help, projects, tasks

```sh
./gradlew help
./gradlew --help
./gradlew help --task assemble
./gradlew projects
./gradlew tasks
```





## Taming the Daemons

```sh
./gradlew --status
```

```sh
./gradlew --stop
```





## Run faster: Abbreviations!

```sh
# instead of this:
./gradlew :app:assembleDemoDebug
# you can write this:
./gradlew assDDeb
# or even this:
./gradlew aDeb

# this will not work, but show all matching candidates:
./gradlew aD
# will print: Task 'aD' is ambiguous [...]
#   Candidates are: 'androidDependencies', 'assembleDebug', 'assembleDemo'.
```





## Run faster: Auto-complete!

Set up: https://github.com/gradle/gradle-completion

First run will take longer, then: fast command-line auto-completion!

For example: `./gradlew bui` + TAB


### Note: does not work for uppercase project names! (PR open)

```bash
# currently:
^([[:lower:]][[:alnum:][:punct:]]*)([[:space:]]-[[:space:]]([[:print:]]*))?

# should be this instead:
^([[:alpha:]][[:alnum:][:punct:]]*)([[:space:]]-[[:space:]]([[:print:]]*))?
```

Gradle team: it would be nice if someone of you could merge
[PR 120](https://github.com/gradle/gradle-completion/pull/120) 🙏 





## `--continuous`

Run a task:

```sh
./gradlew okhttp:test
```

Run continuosly on file changes:

```sh
./gradlew okhttp:test --continuous
```

This will now run on *every* file change of the affected modules! 🤯

With a single test class:

```sh
./gradlew :okhttp:jvmTest --tests okhttp3.ConnectionReuseTest --continuous
```

### Example code changes

1. Change `ConnectionReuseTest.silentRetryWhenIdempotentRequestFailsOnReusedConnection`
2. add `port + Random.nextInt(2)` in `MockWebServer.kt`
3. Change unrelated file: `AndroidAsyncDns.kt` from `okhttp-android` module

Would wish for IDE support 🙏





## "Wait, this works?"

To show all Gradle tasks in 'Gradle' view:

Settings -> Experimental ->
  Configure all Gradle tasks during Gradle Sync (this can make Gradle Sync slower)

```kotlin
subprojects {
    val nameUpper = name.uppercase()
    println(nameUpper)
}
```

```kotlin
subprojects {
    val project = this
    val nameUpper = project.name.uppercase()
    println(nameUpper)
}
```





## Profile your builds

Install: https://github.com/gradle/gradle-profiler

Basic usage:

```sh
gradle-profiler --benchmark help
```

Now let's create a `okhttp_help.scenarios` file and compare tasks:

```plain
default-scenarios = ["global_help", "module_help"]

global_help {
    title = "Global help"
    tasks = ["help"]
}

module_help {
    title = "Module help"
    tasks = ["okhttp:help"]
}
```





## Benchmark your projects - with K2!

[Kotlin 1.9.0](https://kotlinlang.org/docs/whatsnew-eap.html#new-kotlin-k2-compiler-updates) comes with a new flag `-Pkotlin.experimental.tryK2=true` for trying out K2! 🌟

- Note: Use `cleanup-tasks = ["clean"]`
- Note: Use `gradle-args = ["--no-build-cache"]` to ignore build cache

Benchmark (using `kotlin1.9` branch):

```sh
gradle-profiler --benchmark --scenario-file ../talk/okhttp_k2.scenario
```

- Tip: Use smaller numbers like `warm-ups = 1` and `iterations = 1` to test your `.scenarios` file before you run a full benchmark (building big projects could take time!)
- Tip: you can also set these from the commandline: `--warmups=2 --iterations=3`





## Benchmark 2 Git branches

Using `git-checkout = {...}` block you can jump between Git branches during the benchmark!

```sh
gradle-profiler --benchmark --scenario-file ../talk/okhttp_different_branches.scenarios
```





## Profile incremental builds

Show modifications to nowinandroid: added `common-impl` module

Tip: Use https://github.com/vanniktech/gradle-dependency-graph-generator-plugin to create dependency graph!

```sh
./gradlew generateProjectDependencyGraph --no-configuration-cache
```

To create artificial ABI changes, we can use:

```plain
apply-abi-change-to = "path/to/File.kt"
```

Check out all the cool options on https://github.com/gradle/gradle-profiler#profiling-incremental-builds 🎉



Note: this does currently not work together with `git-checkout`, see:
https://github.com/gradle/gradle-profiler/issues/325





## GitHub PRs

Tip: Add labels like `Benchmark build` to benchmark your PRs against main branch!
