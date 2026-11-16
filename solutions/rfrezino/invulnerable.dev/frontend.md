---
type: Service
title: Frontend (Vue 3 + Vite)
tags: [invulnerable-dev]
description: Single-page landing site composed of section components.
resource: frontend/
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
---
Vue ^3.5, Vite 7, TypeScript, vue-tsc. Scripts: `dev`, `build` (type-check then build), `preview`. `App.vue` renders Header, then in `main` the sections in order: Hero, ProblemSolution, ProductShowcase, HowItWorks, WhyItMatters, Testimonials, AboutUs, Pricing, FAQ, then SiteFooter. See [landing sections](/components/landing-sections.md).
