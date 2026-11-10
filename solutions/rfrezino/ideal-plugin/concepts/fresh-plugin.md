---
type: "Project"
title: "Fresh IntelliJ plugin"
description: "IntelliJ Platform plugin that locates, creates and opens Python test files for a source file."
---
# Overview
Plugin id `fresh.com.freshbooks.fresh`, name "Fresh", described as "Helps locating, creating and moving test files". Written in Kotlin and built with Gradle.

# Build
- Kotlin JVM 1.6.20, `org.jetbrains.intellij` 1.5.2, JVM target 11.
- Targets IntelliJ Community 2021.2; sinceBuild 212, untilBuild 241.*.
- Version 1.1.3-SNAPSHOT, group `fresh.com.freshbooks`.
- Signing and publishing read env vars CERTIFICATE_CHAIN, PRIVATE_KEY, PRIVATE_KEY_PASSWORD, PUBLISH_TOKEN.

# Parts
- [Test file navigation](/concepts/test-file-navigation.md)
- [Test actions](/concepts/test-actions.md)
- [Floating toolbar](/concepts/floating-toolbar.md)
