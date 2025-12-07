# DeepSeek-OCR API Documentation

## Base URL
```
http://localhost:8001
```

## Endpoints

### 1. Health Check
```http
GET /health
```

**Response:**
```json
{
  "status": "healthy",
  "backend": "cuda",
  "platform": "Linux",
  "model_loaded": true
}
```

### 2. OCR Recognition (Single Image)
```http
POST /ocr
Content-Type: multipart/form-data
```

**Parameters:**
- `file` (required): Image file (PNG, JPG, JPEG)
- `mode` (optional): Recognition mode
  - `doc` - Document to Markdown (default)
  - `ocr` - General OCR
  - `plain` - Plain text
  - `chart` - Chart parser
  - `image` - Image description
  - `find` - Find & locate
  - `custom` - Custom prompt
- `search_text` (optional): Search text for `find` mode
- `custom_prompt` (optional): Custom prompt for `custom` mode

**Response:**
```json
{
  "text": "Recognized text...",
  "bboxes": [
    {"text": "found text", "bbox": [x1, y1, x2, y2]}
  ],
  "image_base64": "data:image/png;base64,..."
}
```

### 3. PDF OCR (All Pages) ⭐ NEW
```http
POST /ocr-pdf
Content-Type: multipart/form-data
```

**Parameters:**
- `file` (required): PDF file
- `prompt_type` (optional): Recognition mode (same as `/ocr`)
- `find_term` (optional): Search text for find mode
- `custom_prompt` (optional): Custom prompt

**Response:**
```json
{
  "success": true,
  "filename": "document.pdf",
  "page_count": 5,
  "pages": [
    {
      "page": 1,
      "text": "Page 1 content...",
      "raw_text": "..."
    },
    {
      "page": 2,
      "text": "Page 2 content...",
      "raw_text": "..."
    }
  ],
  "merged_text": "--- Page 1 ---\nContent...\n--- Page 2 ---\nContent...",
  "metadata": {
    "mode": "document",
    "backend": "cuda"
  }
}
```

### 4. PDF to Images
```http
POST /pdf-to-images
Content-Type: multipart/form-data
```

**Parameters:**
- `file` (required): PDF file

**Response:**
```json
{
  "success": true,
  "images": [
    {
      "data": "data:image/png;base64,...",
      "name": "page_1.png",
      "width": 1200,
      "height": 1600,
      "page_number": 1
    }
  ],
  "page_count": 5
}
```

## Python Client Example

```python
import requests
import base64

# Simple OCR (single image)
with open("image.png", "rb") as f:
    response = requests.post(
        "http://localhost:8001/ocr",
        files={"file": f},
        data={"mode": "ocr"}
    )
    result = response.json()
    print(result["text"])

# PDF OCR (all pages) ⭐ NEW
with open("document.pdf", "rb") as f:
    response = requests.post(
        "http://localhost:8001/ocr-pdf",
        files={"file": f},
        data={"prompt_type": "document"},
        timeout=600  # PDF processing takes longer
    )
    result = response.json()
    
    # Get merged text from all pages
    print(result["merged_text"])
    
    # Or process each page separately
    for page in result["pages"]:
        print(f"Page {page['page']}: {page['text'][:100]}...")

# Find mode with search
with open("invoice.png", "rb") as f:
    response = requests.post(
        "http://localhost:8001/ocr",
        files={"file": f},
        data={"mode": "find", "search_text": "Total"}
    )
    result = response.json()
    for bbox in result.get("bboxes", []):
        print(f"Found: {bbox['text']} at {bbox['bbox']}")
```

## cURL Examples

```bash
# Health check
curl http://localhost:8001/health

# Simple OCR (single image)
curl -X POST http://localhost:8001/ocr \
  -F "file=@image.png" \
  -F "mode=ocr"

# PDF OCR (all pages) ⭐ NEW
curl -X POST http://localhost:8001/ocr-pdf \
  -F "file=@document.pdf" \
  -F "prompt_type=document" \
  --max-time 600

# Find mode
curl -X POST http://localhost:8001/ocr \
  -F "file=@invoice.png" \
  -F "mode=find" \
  -F "search_text=Total"

# Custom prompt
curl -X POST http://localhost:8001/ocr \
  -F "file=@document.png" \
  -F "mode=custom" \
  -F "custom_prompt=Extract all dates and amounts"
```

## Error Responses

```json
{
  "detail": "Error message"
}
```

Status codes:
- `200` - Success
- `400` - Bad request
- `500` - Server error
