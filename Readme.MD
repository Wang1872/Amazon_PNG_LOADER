# Amazon Product Photo Downloader

## 📋 Project Overview

A lightweight, command-line utility for automated downloading and organization of product images from Amazon product pages. Extract all product photos and A+ Enhanced Content images with a simple, intuitive interface.

### Key Features
- ✅ Download all product gallery images in highest resolution
- ✅ Extract A+ Content (Enhanced Content) images  
- ✅ Support for single and batch URL processing
- ✅ Automatic image organization per URL
- ✅ PNG format output with organized file structure
- ✅ Cross-platform compatibility (Windows, macOS, Linux)

---

## 📊 Requirements Analysis

### 1. Gathering & Analysis of Requirement

| Aspect | Specification | Implementation |
|--------|---------------|-----------------|
| **Use Case** | Download all photos of an Amazon product site | Automated image extraction from HTML/JSON |
| **User Interface** | Simple and intuitive | CLI with clear prompts and progress indicators |
| **Input Type** | Single line of Amazon site URL OR multiple URLs | Command-line arguments + file input support |
| **Output Format** | All PNG images (including product + A+) | Auto-detect format, preserve quality |
| **Organization** | Sorted individually for each URL | Separate ZIP per product with sequential naming |

### 2. System Architecture

```
Input (Amazon URLs)
    ↓
URL Validation
    ↓
Page Fetch (HTML)
    ↓
Image Detection
  ├─ Product Gallery Images
  ├─ Thumbnail Images
  └─ A+ Content Images
    ↓
Image Download & Processing
    ↓
Organization (per URL)
    ↓
ZIP Archive Creation
    ↓
Output (Organized Archives)
```

---

## 🎯 Functional Requirements

### FR1: Image Discovery
- **Requirement**: Detect and extract all image URLs from Amazon product pages
- **Scope**: 
  - Product gallery images (main product images)
  - Thumbnail gallery images
  - A+ Content/Enhanced Content images
  - High-resolution variants only

### FR2: Image Download
- **Requirement**: Download images with error handling and retry logic
- **Scope**:
  - Support multiple simultaneous downloads
  - Handle HTTP errors gracefully
  - Preserve original image quality
  - Avoid blocking/rate limiting


### FR3: Output Organization
- **Requirement**: Organize output per URL with clear structure
- **Scope**:
  - One ZIP archive per product
  - Archive naming: `Amazon_[ProductName]_[ASIN]_Photos.zip`
  - Separate directory per URL
  - Manifest file with image metadata (optional)

### FR4: Batch Processing
- **Requirement**: Support single and multiple URL input
- **Scope**:
  - Command-line argument URLs
  - File input (one URL per line)
  - Combined input methods
  - Parallel processing with rate limiting

---

## 📋 Detailed Specifications

### Input Specification

#### Option 1: Single URL via Command Line
```bash
amazon-dl https://www.amazon.de/dp/B0123456789
```

#### Option 2: Multiple URLs via Command Line
```bash
amazon-dl https://www.amazon.de/dp/B111 https://www.amazon.de/dp/B222 https://www.amazon.com/dp/B333
```

#### Option 3: File Input (One URL per Line)
```bash
amazon-dl -f urls.txt
amazon-dl --file product_links.txt
```

#### Option 4: Combined Input
```bash
amazon-dl -f urls.txt https://www.amazon.de/dp/B444 -o ./downloads
```

### Output Specification

#### Directory Structure
```
amazon_downloads/
├── Amazon_Samsung_SSD_B089G6YHN5_Photos/
│   ├── image_001.png
│   ├── image_002.png
│   ├── image_003.png
│   └── metadata.json (optional)
└── Amazon_Sony_Headphones_B08F7D4KQ8_Photos.zip
    └── (contains all images for second product)
```

#### Image Naming Convention
- Format: `image_XXX.png` (3-digit padding)
- Order: Sequential based on discovery (gallery first, then A+ content)
- Duplicates: Automatically deduplicated by image ID

#### Metadata Output (Optional)
```json
{
  "product_name": "Samsung 970 EVO Plus",
  "asin": "B089G6YHN5",
  "url": "https://www.amazon.de/dp/B089G6YHN5",
  "download_timestamp": "2024-01-15T14:23:45Z",
  "total_images": 12,
  "images": [
    {
      "filename": "image_001.png",
      "original_url": "https://...",
      "source": "product_gallery",
      "size_bytes": 245678
    }
  ]
}
```

---

## 🔧 Technical Requirements

### Non-Functional Requirements

#### Performance
- Single product download: 3-5 seconds
- Batch of 10 products: 1-2 minutes
- Batch of 100 products: 10-15 minutes
- Rate limiting: 0.5-1 second delay between downloads

#### Reliability
- Automatic retry on failed downloads (max 3 attempts)
- Graceful error handling (skip failed images, continue processing)
- Connection timeout handling (10 seconds default)
- Log all errors to console and optional log file

#### Compatibility
- Python 3.8+
- Windows, macOS, Linux
- All major Amazon locales (DE, COM, CO.UK, FR, IT, ES, COM.AU)

#### Security
- HTTPS only (no HTTP)
- User-Agent spoofing to avoid blocking
- No credential storage or authentication required
- Privacy: Local processing, no data sent to external servers

---

## 🚀 Installation & Quick Start

### Prerequisites
- Python 3.8 or higher
- pip (Python package manager)
- Internet connection

### Installation

```bash
# 1. Clone or download the project
cd amazon-product-photo-downloader

# 2. Install dependencies
pip install -r requirements.txt

# 3. Verify installation
python amz_loader.py --help
```

### Quick Start Examples

**Example 1: Single Product**
```bash
python amz_loader.py https://www.amazon.de/dp/B089G6YHN5
```

**Example 2: Multiple Products**
```bash
python amz_loader.py \
  https://www.amazon.de/dp/B089G6YHN5 \
  https://www.amazon.com/dp/B08F7D4KQ8 \
  https://www.amazon.co.uk/dp/B08R46H8LS
```

**Example 3: Batch Download from File**
```bash
echo "https://www.amazon.de/dp/B089G6YHN5" > urls.txt
echo "https://www.amazon.com/dp/B08F7D4KQ8" >> urls.txt

python amz_loader.py -f urls.txt
```

**Example 4: Custom Output Directory**
```bash
python amz_loader.py \
  -o ./amazon_backup \
  -f urls.txt
```

---

## 📖 Usage Guide

### Command-Line Interface

```
usage: amz_loader.py [-h] [-f FILE] [-o OUTPUT] [urls ...]

Automated Amazon product photo downloader with A+ content extraction

positional arguments:
  urls                  Amazon product URLs (ASIN format: /dp/XXXXXX)

options:
  -h, --help            show this help message and exit
  -f FILE, --file FILE  Text file with URLs (one per line)
  -o OUTPUT, --output OUTPUT
                        Output directory (default: amazon_downloads)
  -v, --verbose         Enable verbose logging
```

### Real-World Scenarios

#### Scenario 1: Monitor Product Competitors
```bash
# Create competitors.txt
https://www.amazon.com/dp/B111  # Competitor A
https://www.amazon.com/dp/B222  # Competitor B
https://www.amazon.com/dp/B333  # Competitor C

# Download all photos
python amz_loader.py -f competitors.txt -o ./competitors_photos
```

#### Scenario 2: Backup Product Listings
```bash
# Backup current product images (scheduled daily)
python amz_loader.py -f my_products.txt -o ./daily_backup_$(date +%Y-%m-%d)
```

#### Scenario 3: Research & Analysis
```bash
# Analyze product photography from multiple sellers
python amz_loader.py \
  https://www.amazon.de/dp/B111 \
  https://www.amazon.de/dp/B222 \
  https://www.amazon.de/dp/B333 \
  -o ./research/seller_comparison
```

---

## 📦 Project Structure

```

```

---

## 🔍 Supported Amazon Locales



---

## ⚙️ Configuration Options

### Environment Variables (Optional)



---

## 🐛 Troubleshooting

### Common Issues & Solutions

| Issue | Cause | Solution |
|-------|-------|----------|
| `ModuleNotFoundError: No module named 'requests'` | Missing dependencies | `pip install -r requirements.txt` |
| `No images found on page` | Page not fully loaded | Ensure URL is valid product page (contains `/dp/ASIN`) |
| `SSL: CERTIFICATE_VERIFY_FAILED` | SSL certificate issue | Update Python or use: `pip install --upgrade certifi` |
| `HTTP 403 Forbidden` | IP blocked by Amazon | Wait 24 hours or use VPN, increase delay in config |
| `Connection timeout` | Network issue | Check internet connection, increase timeout value |
| `Permission denied` | File system permissions | Check folder write permissions: `chmod 755 ./amazon_downloads` |

### Debugging

Enable verbose logging for detailed information:
```bash
python amz_loader.py -v -f urls.txt
```

Check log file for errors:
```bash
tail -f downloader.log
```

---


## 📄 License

This project is provided as-is for personal and research use only. 

**Disclaimer**: This tool is designed for personal backup and research purposes. Ensure you have the right to download and use these images according to Amazon's Terms of Service and copyright laws.

---

## 🤝 Contributing

### How to Report Issues
1. Check existing issues first
2. Provide detailed error messages
3. Include: OS, Python version, URL (anonymized if needed)
4. Attach relevant error logs

### Feature Requests
- Describe the use case
- Explain expected behavior
- Suggest implementation approach

---

## 🎓 Examples & Use Cases

### Use Case 1: E-Commerce Product Listing
**Goal**: Extract product images for new marketplace listing


### Use Case 2: Competitive Analysis
**Goal**: Analyze competitor photography strategies


### Use Case 3: Content Creation
**Goal**: Gather images for product review content


### Use Case 4: Archive & Backup
**Goal**: Regular backup of owned product listings
```bash
# Scheduled daily via cron/Task Scheduler
python amz_loader.py -f my_products.txt -o ./backups/$(date +%Y-%m-%d)
```

---

## 📈 Statistics & Metrics

Current Implementation:


---

## 🔐 Security & Privacy

- ✅ No data collection or telemetry
- ✅ Local processing only
- ✅ No external API calls (except Amazon CDN for images)
- ✅ No credential storage
- ✅ HTTPS only
- ✅ No user tracking

---

## 📚 Additional Resources

### Related Documentation
- [Python requests library](https://docs.python-requests.org/)
- [BeautifulSoup4 docs](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
- [Amazon Product URL format](https://www.amazon.com/help/customer/display.html)

### Useful Tools
- ImageMagick (advanced image processing)
- ffmpeg (batch image conversion)
- cURL (manual image downloads)

---

**Last Updated**: 
**Version**: 
**Status**: 