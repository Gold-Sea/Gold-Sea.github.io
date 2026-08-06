# zilizhang.site

Personal academic homepage of **Zili Zhang**, served by GitHub Pages at [zilizhang.site](https://zilizhang.site).

Built on the [academic-homepage](https://github.com/luost26/academic-homepage) Jekyll template by [@luost26](https://github.com/luost26), with the site structure adapted from [PKUFlyingPig's homepage](https://github.com/PKUFlyingPig/PKUFlyingPig.github.io).

## Local development

```bash
bundle install
bundle exec jekyll serve
```

Then open <http://127.0.0.1:4000>.

## Content layout

- `_data/profile.yml` — name, bio, education, experience, awards, teaching, service
- `_publications/<year>/<paper>.md` — one file per publication (`selected: true` shows it on the homepage)
- `_data/authors.yml` — author display rules (bold / links)
- `files/` — PDFs (CV, slides) kept at stable URLs

## License

Code is released under the [MIT License](LICENSE) (template by Shitong Luo). Site content — text, publication data, images, and PDFs — © Zili Zhang.
