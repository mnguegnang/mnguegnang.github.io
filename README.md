# G. M. Nguegnang — Personal Academic Website

Personal portfolio and academic website of **Gabin Maxime Nguegnang**, an Applied Mathematics Researcher and AI Consultant specializing in deep learning optimization, LLM alignment, and post-training strategies (RLHF, RLVR, Rubric development).

🔗 **Live site:** [https://mnguegnang.github.io](https://mnguegnang.github.io)

---

## What's on the Site

| Section | Description |
|---------|-------------|
| **About** | Professional summary, current research focus, and academic background |
| **Portfolio** | Project showcases — RAG Chatboard, post-training strategies, neural network convergence analysis, fuel consumption prediction system |
| **Publications** | Published papers in Springer Nature and SampTA conference proceedings |
| **Experience** | Professional history across AI consulting, doctoral research, and ML engineering |
| **CV** | Downloadable PDF curriculum vitae |

## Built With

- [Jekyll](https://jekyllrb.com/) — static site generator
- [Academic Pages](https://github.com/academicpages/academicpages.github.io) — Jekyll template for academic portfolios (forked from [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/))
- [GitHub Pages](https://pages.github.com/) — hosting and continuous deployment

## Running Locally

1. Install prerequisites:
   ```bash
   # Linux / WSL
   sudo apt install ruby-dev ruby-bundler nodejs

   # macOS
   brew install ruby node && gem install bundler
   ```

2. Install dependencies:
   ```bash
   bundle install
   ```

3. Serve the site:
   ```bash
   bundle exec jekyll serve -l -H localhost
   ```
   The site will be available at `http://localhost:4000`.

### Using Docker

```bash
docker compose up
```

The site will be available at `http://localhost:4000`.

## Repository Structure

```
├── _config.yml          # Site-wide settings (title, author, theme)
├── _pages/              # Top-level pages (about, CV, portfolio, experience)
├── _portfolio/          # Project write-ups rendered in the Portfolio section
├── _publications/       # Publication entries
├── _posts/              # Blog posts
├── _teaching/           # Teaching entries
├── _data/               # Navigation, CV JSON, author metadata
├── files/               # Downloadable assets (CV PDF, paper PDFs, BibTeX)
├── images/              # Site images and figures
└── _sass/               # Theme stylesheets
```

## License

The template is released under the [MIT License](LICENSE). Site content (text, images, and publications) is © Gabin Maxime Nguegnang.

---

> **Design rationale:** This README follows GitHub's recommended practices for repository documentation — it states the project's purpose, links to the live site, lists technologies used, provides setup instructions, and describes the repository layout. This helps contributors and visitors quickly understand the project without navigating the codebase (see [GitHub Docs: About READMEs](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes) and [Make a README](https://www.makeareadme.com/)).
