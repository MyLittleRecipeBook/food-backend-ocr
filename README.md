# OCR Receipt Parser for Coupang and Naver Orders

This FastAPI application provides automated optical character recognition (OCR) and text extraction for Coupang and Naver order receipts. It simplifies the process of extracting purchased item information from receipt images, making it easier to track and analyze order history.

The application leverages PaddleOCR for accurate Korean text recognition and implements specialized filtering algorithms for both Coupang and Naver receipt formats. It provides a REST API interface that accepts image uploads and returns structured order information, making it suitable for integration into larger e-commerce analytics or order management systems.

## Repository Structure
```
.
├── Dockerfile              # Container configuration for deployment
├── main.py                # FastAPI application with OCR and filtering logic
└── .gitignore             # Git version control ignore patterns
```

## Usage Instructions
### Prerequisites
- Docker installed on your system
- Python 3.7 or higher (if running locally)
- Sufficient storage space for OCR models (~500MB)

Required Python packages:
```
paddlepaddle
paddleocr
fastapi
uvicorn
```

### Installation

#### Using Docker (Recommended)
```bash
# Build the Docker image
docker build -t ocr-receipt-parser .

# Run the container
docker run -p 8000:8000 ocr-receipt-parser
```

#### Local Installation
```bash
# Create and activate virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install paddlepaddle paddleocr fastapi uvicorn

# Start the application
uvicorn main:app --host 0.0.0.0 --port 8000
```

### Quick Start
1. Start the server using either Docker or local installation
2. The API will be available at `http://localhost:8000`
3. Use the following endpoints:
   - `/coupang/`: Process Coupang receipt images
   - `/naver/`: Process Naver receipt images

Example API usage:
```python
import requests

# Upload Coupang receipt
with open('receipt.jpg', 'rb') as f:
    response = requests.post('http://localhost:8000/coupang/', 
                           files={'file': f})
print(response.json())
```

### More Detailed Examples

#### Processing Coupang Receipts
```python
# Response format for Coupang receipts
{
    "filename": "coupang_receipt.jpg",
    "result": ["Product Name 1", "Product Name 2"]
}
```

#### Processing Naver Receipts
```python
# Response format for Naver receipts
{
    "filename": "naver_receipt.jpg",
    "result": ["Product Name 1", "Product Name 2"]
}
```

### Troubleshooting

#### Common Issues

1. OCR Not Detecting Text
   - Ensure image quality is sufficient
   - Check if image format is supported (JPG, PNG)
   - Verify image is properly oriented
   - Enable debug logging:
     ```python
     import logging
     logging.basicConfig(level=logging.DEBUG)
     ```

2. Docker Container Issues
   - Verify port mapping: `docker ps`
   - Check container logs: `docker logs <container_id>`
   - Ensure sufficient system resources

3. Performance Optimization
   - Monitor memory usage during OCR processing
   - Consider enabling GPU support if available
   - Implement image preprocessing for better OCR results

## Data Flow
The application processes receipt images through OCR and filtering to extract relevant order information. Images are processed sequentially through text extraction and specialized filtering based on the receipt type.

```ascii
Upload Image → OCR Processing → Text Extraction → Receipt-Specific Filtering → JSON Response
     ↑                 ↓                ↓                     ↓                    ↓
[HTTP POST]    [PaddleOCR Engine]  [Raw Text]    [Coupang/Naver Filter]    [Product Names]
```

Key component interactions:
- FastAPI handles HTTP requests and file uploads
- PaddleOCR performs text extraction with Korean language support
- Filter functions process raw text based on receipt format
- Results are returned as JSON responses
- Images are temporarily stored in platform-specific folders
- Error handling is implemented at each processing stage