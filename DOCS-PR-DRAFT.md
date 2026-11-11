# Docs PR draft — DO NOT OPEN until the package is on PyPI

Prepared 2026-11-11. Target repo: assafelovic/gpt-researcher, branch off
`main`. The maintainer's condition (2026-11-09) was: "Once an AnySearch
package is on PyPI, a docs PR adding it to the community retrievers table
is very welcome."

## Target file

`docs/docs/gpt-researcher/search-engines/retriever-plugins.md`

## Change 1 — community retrievers table (after the Sciverse row)

```markdown
| AnySearch | [`gpt-researcher-anysearch`](https://pypi.org/project/gpt-researcher-anysearch/) | [PyPI project page](https://pypi.org/project/gpt-researcher-anysearch/) |
```

Note: the PyPI links do not exist yet — this draft is only openable AFTER
`pip install gpt-researcher-anysearch` works. The Maintainer-and-support
column follows the Keenable row's form (PyPI project page); a GitHub repo
link belongs here instead once the standalone package repo exists — that
repo URL is TBD at publish time and must be filled in before opening.

## Change 2 — `### AnySearch` section (after the `### Sciverse` section, before `## Writing a plugin`)

```markdown
### AnySearch

[AnySearch](https://anysearch.com/) is a unified search service exposing
general and vertical search through one endpoint. It works without an API
key; `ANYSEARCH_API_KEY` is optional for higher limits.

```bash
pip install gpt-researcher-anysearch
export RETRIEVER=anysearch
```

Results carry a title and URL plus a snippet (`requires_scraping = True`),
so GPT Researcher's scraping stage fetches the real page text, like the
other web retrievers. Vertical search (`tag`/`params`, e.g.
`tag="code.doc"`, `params={"library": "react"}`) is available when
constructing the retriever directly in Python; it is not reachable through
the `RETRIEVER` environment variable, which only passes the query and
domain filter.
```

## Suggested PR title/body

Title: `docs: add AnySearch to community retrievers`

Body:

```text
Adds the AnySearch plugin (gpt-researcher-anysearch, RETRIEVER=anysearch)
to the community retrievers table, per the maintainer's note on #2203.

The package is published on PyPI: https://pypi.org/project/gpt-researcher-anysearch/
```

## Open questions before opening (founder's call)

- Standalone package repo URL (for the Maintainer-and-support column and
  the package's own Homepage) — currently TBD; the draft above uses the
  PyPI project page only.
- Live-search status: demonstrated on 2026-11-11 (two live searches, 3
  results each, via the real class — see RECORD-2026-11-11.md §5), but
  the endpoint was unresponsive ~15 minutes earlier the same morning, so
  it is intermittent. The docs text makes no live-search claim, so it is
  safe to open regardless — but say so in the PR body rather than
  implying the endpoint is stable.
