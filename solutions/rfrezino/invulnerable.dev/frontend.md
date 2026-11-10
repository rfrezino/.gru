---
type: "Component"
title: "Frontend landing page"
description: "Vue 3 + Vite + TypeScript single-page landing site"
---
`frontend/src/App.vue` composes, in order inside a header/main/footer layout: Header, HeroSection, ProblemSolution, ProductShowcase, HowItWorks, WhyItMatters, Testimonials, AboutUs, PricingSection, FAQSection, then SiteFooter. Components live in `frontend/src/components/` (plus RotatingMessage and icon components under `icons/`).

- Stack: Vue ^3.5, Vite 7, TypeScript 5.8, vue-tsc.
- Scripts: `dev`, `build` (type-check + vite build), `preview`.
- Pricing details: [pricing section](/components/pricing-section.md).
- Local run: [development](/development.md).
