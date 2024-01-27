# inko-wobsite

A small and opinionated static site generation library, written in Inko, named
after [xkcd 148](https://xkcd.com/148/).

## Requirements

- Inko main (for now)

## Installation

```bash
inko pkg add github.com/yorickpeterse/inko-wobsite 0.10.0
inko pkg sync
```

## Usage

To get started, create `src/main.inko` with the following contents:

```inko
import wobsite.Site

class async Main {
  fn async main {
    Site.build fn (site) {

    }
  }
}
```

Next, make sure the `./source` directory exists, as this is where your site's
source files are placed:

```bash
mkdir source
```

If you now run `inko run`, the site will be built into the `public` directory.
At this point, nothing useful is built as we haven't defined any rules yet.

To build the site, you can use the following methods on the `Site` type:

- `copy(pattern)`: copies files from the source directory to the output
  directory. This is useful for simply copying files such as CSS or simple text
  files.
- `generate(path, func)`: calls `func` and writes its return value to `path`
  (relative to the output directory). This is useful when generating files using
  arbitrary logic, such as an Atom feed.
- `page(pattern, index, func)`: finds Markdown files matching `pattern`, turning
  them into HTML files by calling the `func` closure.

For example:

```inko
import wobsite.Site

class async Main {
  fn async main {
    Site.build fn (site) {
      site.copy('*.css')
    }
  }
}
```

Here `site.copy('*.css')` tells the site generator to copy any `.css` files
found in `source` (or any of its sub directories) into the `public` directory,
using the same hierarchy as the source directory (e.g. `source/css/icons.css`
becomes `public/css/icons.css`).

### Converting Markdown to HTML

Generating an HTML file from a Markdown file is a little more involved:

```inko
import wobsite.(Site, Page)

class async Main {
  fn async main {
    Site.build fn (site) {
      site.page('/index.md', index: false) fn {
        recover fn (_, page: Page) { Result.Ok(page.to_html([])) }
      }
    }
  }
}
```

This turns `source/index.md` into `public/index.html` by parsing `index.md` and
turning its Markdown into HTML, without wrapping it in a layout of sorts.

Markdown files are required to use this format:

```markdown
---
{
  "title": "TITLE HERE",
  "date": "YYYY-MM-DDTHH:MM:SSZ"
}
---

The body goes here.
```

For example:

```markdown
---
{
  "title": "This is my website",
  "date": "2014-02-04T13:00:00Z"
}
---

This is the homepage of my wobsite. Cool!
```

The `title` key is required, but the `date` key is optional.

For a more in-depth example, refer to the source code of [my personal
website](https://github.com/yorickpeterse/yorickpeterse.com/blob/main/src/main.inko).

## License

All source code in this repository is licensed under the Mozilla Public License
version 2.0, unless stated otherwise. A copy of this license can be found in the
file "LICENSE".
