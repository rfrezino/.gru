---
tags: [intellij, plugin]
type: Build Configuration
title: Gradle build
description: Gradle Kotlin DSL build using the IntelliJ platform plugin.
resource: build.gradle.kts
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
---
- Plugins: java, Kotlin JVM 1.6.20, org.jetbrains.intellij 1.5.2.
- Group `fresh.com.freshbooks`, version 1.1.3-SNAPSHOT.
- Target IDE: IntelliJ Community (IC) 2021.2; sinceBuild 212, untilBuild 241.*; JVM 11.
- Signing and publishing read env vars CERTIFICATE_CHAIN, PRIVATE_KEY, PRIVATE_KEY_PASSWORD, PUBLISH_TOKEN.

Registers [plugin.xml](/components/plugin-xml.md).
