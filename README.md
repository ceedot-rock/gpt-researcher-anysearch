# gpt-researcher-anysearch

AnySearch retriever plugin for [GPT Researcher](https://github.com/assafelovic/gpt-researcher)
— use AnySearch as a search provider via `RETRIEVER=anysearch`, with no change
to GPT Researcher itself.

This is the standalone-package form of the retriever originally submitted as
[PR #2203](https://github.com/assafelovic/gpt-researcher/pull/2203). Per the
maintainer's direction (2026-11-09), new search providers ship as their own
package registered under the `gpt_researcher.retrievers` entry-point group —
see [Retriever plugins](https://docs.gptr.dev/docs/gpt-researcher/search-engines/retriever-plugins).
PR #2203 was closed unmerged in favour of this package; a docs PR adding this
package to the community retrievers table follows once it is on PyPI.

## Install

```bash
pip install gpt-researcher-anysearch
export RETRIEVER=anysearch
```

It works without an API key — anonymous requests are served within
AnySearch's server-side quota. Optionally set a key for higher limits:

```bash
export ANYSEARCH_API_KEY=<redacted>
```

## Use

```bash
export RETRIEVER=anysearch
# optional: RETRIEVER=anysearch,duckduckgo to combine with a built-in
```

Or directly in Python:

```python
from anysearch_retriever import AnySearchRetriever

retriever = AnySearchRetriever(
    "GPT Researcher open source AI research agent",
    tag="code.doc",                 # optional vertical tag, e.g. "code.doc"
    params={"library": "react"},   # optional vertical parameters
)
results = retriever.search(max_results=5)
# -> [{"href": ..., "title": ..., "body": ...}, ...]
```

## Behaviour

- Hits `POST https://api.anysearch.com/v1/search` with `query` and
  `max_results` (clamped to the server-side range 1–10). Optional `tag` /
  `params` select a vertical domain (see `GET https://api.anysearch.com/v1/domains`
  for the catalog).
- Sends `X-Anysearch-Client: gpt-researcher-anysearch/1.0` on every request.
- **API key optional.** When `ANYSEARCH_API_KEY` is unset, empty, or
  whitespace-only, the request goes out anonymously with **no**
  `Authorization` header — never an empty `Bearer` token. A key can also be
  passed per-instance via `headers={"anysearch_api_key": ...}`.
- Returns normalized `{href, title, body}` items. `requires_scraping = True`:
  results carry a title/URL plus a snippet and GPT Researcher's scraping
  stage fetches the real page text.
- Errors never raise: HTTP failures, non-zero business `code`, auth failures
  (401/403), rate limits (429), timeouts, and malformed responses are logged
  with a distinct message, recorded on `retriever.last_error`, and return `[]`
  (GPT Researcher's existing convention). An empty list therefore always means
  the query genuinely returned nothing. The server `request_id` is captured on
  `retriever.last_request_id` for diagnostics.
- The API key never appears in logs, error messages, or snapshots (covered by
  test).

## Live-search status

At the original PR's integration-test time (2026-10-10), the upstream
`POST /v1/search` endpoint did not respond (six attempts, timeouts up to
100s, zero bytes; TLS handshake fine; `GET /v1/domains` and
`GET /v1/sub-domains` returned 200). During this package build on
2026-11-11 the endpoint was unresponsive again at ~10:15 EST (two fresh
anonymous attempts, 30s and 60s timeouts, zero bytes) — then **responsive
by ~10:28 EST**: two live searches through the real class returned 3 real
results each (gptr.dev, github.com/assafelovic/gpt-researcher,
opensourcealternatives.to), 2.4s and ~6.9s, no errors. The endpoint is
intermittent; a successful live search is demonstrated, stability is not
claimed. Full detail in `RECORD-2026-11-11.md` §5.

## Tests

```bash
python -m unittest discover -s tests -v
```

All HTTP is mocked; no network calls. The two registration tests require
`gpt-researcher` and this package installed in the same environment (they
exercise the entry-point discovery). 17 tests total.

## License

MIT — see `LICENSE`.
