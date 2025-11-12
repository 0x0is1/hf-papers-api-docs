# Hugging Face Papers API Documentation

This document describes the public API endpoints that power the **Daily Papers** feature on [huggingface.co](https://huggingface.co).
These endpoints provide access to research papers, metadata, and daily-curated listings derived from arXiv and other scientific sources.

The API is publicly available for read-only operations and is used internally by the Hugging Face frontend (built with SvelteKit) to render the Papers interface.

---

## Overview

The Daily Papers API enables:

* Keyword-based paper search
* Retrieval of detailed paper metadata
* Paginated access to daily-curated papers

All endpoints return data in JSON format and are accessible without authentication.

---

## Base URL

```
https://huggingface.co
```

All API routes are relative to this base domain.

---

## Available Endpoints

| Endpoint                 | Method | Description                               | Pagination            |
| ------------------------ | ------ | ----------------------------------------- | --------------------- |
| `/api/papers/search`     | GET    | Search for papers by keyword.             | No                    |
| `/api/papers/{arxiv_id}` | GET    | Retrieve details for a specific paper.    | N/A                   |
| `/api/daily_papers`      | GET    | Retrieve curated papers for a given date. | Yes (`page`, `limit`) |

---

## 1. `GET /api/papers/search`

### Description

Searches the papers database by title, abstract, or author.
This endpoint returns a limited number of results (usually 20–25).
Pagination parameters are currently not supported.

### Example Request

```
GET https://huggingface.co/api/papers/search?q=transformer
```

### Query Parameters

| Parameter | Type    | Required | Description                                  |
| --------- | ------- | -------- | -------------------------------------------- |
| `q`       | string  | Yes      | Search keyword (title, abstract, or author). |
| `limit`   | integer | No       | Currently ignored.                           |
| `offset`  | integer | No       | Currently ignored.                           |

---

## 2. `GET /api/papers/{arxiv_id}`

### Description

Retrieves metadata and detailed information for a specific paper by its arXiv ID.

### Example Request

```
GET https://huggingface.co/api/papers/2411.04567
```

### Path Parameters

| Parameter  | Type   | Required | Description                         |
| ---------- | ------ | -------- | ----------------------------------- |
| `arxiv_id` | string | Yes      | The arXiv identifier for the paper. |
---

## 3. `GET /api/daily_papers`

### Description

Retrieves a paginated list of curated papers for a specific date.
This endpoint supports pagination through the `page` and `limit` parameters.

### Example Request

```
GET https://huggingface.co/api/daily_papers?date=2025-11-10&page=1&limit=1
```

### Query Parameters

| Parameter | Type    | Required | Description                                    | Example      |
| --------- | ------- | -------- | ---------------------------------------------- | ------------ |
| `date`    | string  | Yes      | Date for which to fetch papers (`YYYY-MM-DD`). | `2025-11-10` |
| `page`    | integer | No       | Page number, starting from 1.                  | `1`          |
| `limit`   | integer | No       | Number of results per page (default: 10–20).   | `5`          |

## Pagination

The `/api/daily_papers` endpoint implements standard pagination.

## Example Integration

### JavaScript Example

```javascript
async function getDailyPapers(date, page = 1, limit = 5) {
  const response = await fetch(
    `https://huggingface.co/api/daily_papers?date=${date}&page=${page}&limit=${limit}`
  );
  const data = await response.json();
  return data.results;
}

async function getPaperDetails(arxivId) {
  const response = await fetch(`https://huggingface.co/api/papers/${arxivId}`);
  return await response.json();
}
```

### Python Example

```python
import requests

def get_daily_papers(date, page=1, limit=5):
    url = f"https://huggingface.co/api/daily_papers?date={date}&page={page}&limit={limit}"
    response = requests.get(url)
    return response.json()

def get_paper_details(arxiv_id):
    url = f"https://huggingface.co/api/papers/{arxiv_id}"
    response = requests.get(url)
    return response.json()
```

---

## Notes

* The API is public and does not require authentication.
* Responses are cached by the Hugging Face CDN for performance.
* Data originates primarily from the [arXiv API](https://arxiv.org/help/api/).
* `/api/papers/search` is optimized for interactive client-side use and may not expose pagination.

---

## License
Copyright licence by HF is added [here](./LICENSE)

---

### Authors

This documentation was prepared based on public behavior of the Hugging Face `Papers` API and the open endpoints available under [huggingface.co](https://huggingface.co).
Authored and maintained by contributors analyzing the Hugging Face frontend and public Hub API.
