---
site: sandpaper::sandpaper_site
---

This tutorial will guide you through setting up CMS Open data processing using Google Cloud Platform resources.

This is for you if:

- the standard NanoAOD content is not sufficient for your use-case
- you would like to use MiniAOD content for your analysis, but you do not have computing resources available.

The example task is to process a custom NanoAOD starting from MiniAOD. The output of this example task is NanoAOD format enriched with the Particle-Flow (PF) candidates. Part of the existing open data is [available](https://opendata.cern.ch/search?q=&f=experiment%3ACMS&f=file_type%3Ananoaod-pf&l=list&order=desc&p=1&s=10&sort=mostrecent) in this format, and following this example you will be able to process more of it if needed in your analysis.
