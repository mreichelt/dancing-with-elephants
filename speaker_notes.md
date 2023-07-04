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
