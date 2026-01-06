# Complete Weather Satellite Reception & Processing System Plan

**Project:** GEO Satellite Image Capture, Processing & Display System  
**Date Created:** January 2026  
**Target Platforms:** Residential (Fixed) & Portable (Field) Deployments  
**Primary Coverage:** US, EU, Asia, Africa, AU  
**Budget Target:** Low to Mid-Range ($300-800 USD per system)

---

## Table of Contents

1. [System Architecture Overview](#system-architecture)
2. [Part 1: Signal Capture (Antenna Subsystem)](#part-1-signal-capture)
3. [Part 2: Data Processing (Compute Subsystem)](#part-2-data-processing)
4. [Part 3: Web Interface & Display](#part-3-web-interface)
5. [Part 4: OS, Software & Installation](#part-4-os-software)
6. [Part 5: Integration & Deployment](#part-5-integration)
7. [Development Workflow & GitHub Setup](#development-workflow)
8. [Optional Enhancements](#optional-enhancements)
9. [Regional Satellite Frequencies & Details](#regional-frequencies)
10. [Bill of Materials & Cost Estimates](#bill-of-materials)

---

## System Architecture

### Core Concept

```
┌─────────────────────────────────────────────────────────────────────┐
│                     WEATHER SATELLITE SYSTEM                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────────┐                  ┌─────────────────────────┐   │
│  │  SIGNAL CAPTURE │                  │   DATA PROCESSING       │   │
│  │                 │                  │                         │   │
│  │ • Dish Antenna  │──────Raw IQ──────│ • RTL-SDR Demod         │   │
│  │ • LNA Amplifier │    Data Stream   │ • Image Decoding        │   │
│  │ • Filters       │                  │ • Storage & Indexing    │   │
│  │ • RTL-SDR Dongle│                  │ • Time-Lapse Gen        │   │
│  │ • Cabling       │                  │ • Heartbeat Service     │   │
│  │                 │                  │                         │   │
│  └─────────────────┘                  └────────────┬────────────┘   │
│                                                    │                │
│                                    ┌───────────────┴───────────┐    │
│                                    │                           │    │
│                       ┌────────────▼──────────────┐            │    │
│                       │   WEB INTERFACE           │            │    │
│                       │ (HTML5 + JS Frontend)     │            │    │
│                       │ • Image Gallery           │            │    │
│                       │ • Time-Lapse Playback     │            │    │
│                       │ • Event Search            │            │    │
│                       │ • Real-time Updates       │            │    │
│                       └────────────┬──────────────┘            │    │
│                                    │                           │    │
│                       ┌────────────▼──────────────┐            │    │
│                       │   DATABASE & STORAGE      │            │    │
│                       │ • SQLite (Local) or       │            │    │
│                       │   PostgreSQL (Remote)     │            │    │
│                       │ • Image File Storage      │            │    │
│                       │ • Metadata Indexing       │            │    │
│                       └───────────────────────────┘            │    │
│                                                                │    │
│                       ┌─────────────────────────────┐          │    │
│                       │  NETWORK & CONNECTIVITY     │◄─────────┘    │
│                       │ • If Internet: Sync/Upload  │               │
│                       │ • If No Internet: Store     │               │
│                       │ • Heartbeat Service         │               │
│                       └─────────────────────────────┘               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Two Deployment Models

**Model A: Residential/Fixed Installation**
- Permanent mounting on rooftop or pole
- Weatherproof housing
- Stable power supply
- Always-on operation
- Continuous data collection

**Model B: Portable/Field Deployment**
- Portable antenna array in weather-sealed box
- Battery-powered or 12V car power
- Quick setup/teardown (30 mins)
- Mobile data connectivity optional
- Emergency/event response capable

---

## Part 1: Signal Capture

### 1.1 Antenna System

#### GEO Satellite Reception (Primary)

**Frequency:** 1694.1 MHz (GOES-16/18 HRIT), 1692.14 MHz (GK-2A LRIT)  
**Bandwidth:** 1.205 MHz  
**Modulation:** BPSK  
**Polarization:** Linear Vertical

**Recommended Antenna: Parabolic Grid Dish**

| Component | Specification | Notes |
|-----------|---------------|-------|
| Dish Type | 2.4 GHz WiFi Parabolic Grid | Works well at 1.7 GHz |
| Size | 60-90 cm diameter | Larger = better gain |
| Frequency Response | 1.6-2.4 GHz | Works across C-band |
| Gain | 18-24 dBi | Adequate for HRIT |
| Cost | $50-100 USD | Often surplus WiFi equipment |
| Elevation Angle | 20-45° (varies by location) | Use www.dishpointer.com |
| Mounting | Adjustable tripod or roofpole | Needs precise alignment |

**Alternative: Quadrifilar Helix (QHA) for Polar Orbiters**
- Better for 137 MHz polar satellites (NOAA, Meteor)
- Construction: PVC pipe + copper wire
- Cost: ~$30 USD materials
- 360° coverage, omnidirectional

#### Low Noise Amplifier (LNA)

| Component | Specification | Notes |
|-----------|---------------|-------|
| Type | SAWbird+ GOES or similar | Integrated bandpass filter |
| Frequency | 1.6-1.8 GHz | Centered on satellite frequency |
| Noise Figure | 0.6-0.8 dB | Critical for weak signals |
| Gain | 20-25 dB | Boosts weak signals |
| Cost | $60-100 USD | NooElec SAWbird+ recommended |
| Power | 5V via RTL-SDR bias tee | Simplifies cabling |
| Cable | LMR400 or better | <10m optimal, minimize loss |

#### Filters

| Type | Purpose | Specification |
|------|---------|---------------|
| Bandpass Filter | Remove out-of-band interference | 1.6-1.8 GHz, 2 dB insertion loss |
| High-Pass Filter | Block terrestrial FM (88-108 MHz) | Reduces desensitization |
| SAW Filter | Integrated in modern LNAs | Preferred: <0.5 dB loss |

### 1.2 SDR Receiver

**Recommended: RTL-SDR Dongle (NESDR SMArTee XTR)**

| Specification | Value |
|---------------|-------|
| Chipset | Realtek RTL2832U + E4000 tuner |
| Frequency Range | 24 MHz - 1.8 GHz |
| Sample Rate | 2.4 MHz nominal (best for HRIT) |
| Bandwidth | 2.4 MHz (adequate for 1.205 MHz signal) |
| Cost | $30-50 USD |
| Advantages | USB powered, stable, proven |
| Gain Control | Manual or AGC, typically 20-30 dB |

**Alternative: Airspy HF+ Discovery (for HRIT high bandwidth)**
- Wider bandwidth (650 kHz to 80 MHz)
- Better for full HRIT decoding (5 MHz bandwidth potential)
- Cost: $150 USD
- Recommended for serious deployments

### 1.3 Cabling & Connectors

```
Antenna → LNA → Filter → RTL-SDR Dongle
           ↑
       Power (5V)

Cable Requirements:
- LMR400 or equivalent (>10m runs minimize signal loss)
- SMA connectors (standard on most equipment)
- Right-angle adapters for tight spaces
- Ferrite cores on power cables (noise reduction)
- Weatherproof connectors for outdoor runs
```

**Cost-Optimized Cable Run:**
- Short runs (indoor): Standard RG58
- Long runs (rooftop to shack): LMR400 (0.3 dB/m loss @ 1.7 GHz)
- Avoid runs >15m if possible

### 1.4 Antenna Mounting

**Residential Fixed Mount:**
```
Rooftop Pole Mount
├─ 1.25" aluminum mast
├─ Roofpole flash kit (weatherproof)
├─ Guy cables for stability
├─ Adjustable thrust bearing
├─ UV-resistant hardware
└─ Cost: $80-150
```

**Portable Field Box:**
```
Hardened Pelican Case (50L)
├─ Tripod antenna mount (folds for transport)
├─ Retractable cable spools
├─ 12V power distribution
├─ SMA splitter for dual-input capability
├─ Weatherproof cable glands
└─ Total: $100-200
```

---

## Part 2: Data Processing

### 2.1 Compute Hardware

#### Option A: Raspberry Pi 4 (Recommended for Residential)

| Specification | Value | Notes |
|---------------|-------|-------|
| SoC | Broadcom BCM2711 (ARM v8) | 4 cores @ 1.8 GHz |
| RAM | 4-8 GB | 4GB minimum, 8GB ideal for images |
| Storage | MicroSD 64-128 GB | Fast (U3/V30) recommended |
| USB | 4x USB 3.0 | RTL-SDR + backup storage |
| Network | Gigabit Ethernet, WiFi 5 | Wired preferred for reliability |
| Power | 5V 3.5A | USB-C, use good PSU |
| Cost | $50-75 USD | Board only |
| OS Support | 64-bit Raspberry Pi OS | Lightweight Debian variant |

**Residential Setup:**
```
Raspberry Pi 4 (8GB)
├─ 128GB MicroSD (Samsung Pro Plus)
├─ Gigabit Ethernet (wired)
├─ USB Hub (passive, powered)
├─ External SSD (1TB, USB 3.0) for archive
└─ Fanless heatsink + passive cooling
```

#### Option B: Intel NUC or Mini PC (Portable/Higher Performance)

| Component | Specification |
|-----------|---------------|
| CPU | Intel Core i5 or Ryzen 5 (11th gen+) |
| RAM | 16 GB DDR4 |
| Storage | 512GB SSD (OS) + 2TB HDD (data) |
| Dimensions | ~5" cube |
| Power | 19V DC (can use auto PSU) |
| Cost | $400-600 USD |
| Advantage | Faster processing, better for real-time |

**Portable Setup:**
```
Intel NUC or Mini PC
├─ 512GB NVMe (OS + software)
├─ 2TB 2.5" HDD (image storage)
├─ 19V auto power adapter
├─ 12-24V to 19V converter (field use)
└─ Laptop-style carrying case
```

### 2.2 Operating System

**Recommended: Debian 12 (Bookworm) Base Image**

```bash
# Minimal installation (~300 MB)
- No GUI (headless)
- SSH only for management
- Minimal init system (systemd)
- Necessary packages only:
  * Python 3.11+
  * LibUSB (RTL-SDR)
  * FFmpeg (video generation)
  * PostgreSQL client or SQLite
  * CURL/wget (network utilities)
```

**Installation Steps:**
1. Raspberry Pi: Use Raspberry Pi Imager, select "Raspberry Pi OS (64-bit) Lite"
2. Intel NUC: Install Debian 12 netinstall (minimal)
3. First Boot: SSH access only, no desktop

**Initial Configuration:**
```bash
# Secure SSH, disable unnecessary services
sudo systemctl disable bluetooth.service
sudo systemctl disable avahi-daemon.service
sudo systemctl disable ModemManager.service

# Update and harden
sudo apt update && sudo apt dist-upgrade
sudo apt install -y openssh-server ufw curl git
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp    # SSH
sudo ufw allow 80/tcp    # HTTP
sudo ufw allow 443/tcp   # HTTPS (optional)
```

### 2.3 Core Software Stack

#### SDR Signal Demodulation

**Primary: SATDUMP (Modern, Active Development)**

SATDUMP is the recommended approach for 2025+, supporting multiple satellites and formats.

```bash
# Installation
git clone https://github.com/altillimity/SatDump.git
cd SatDump
cmake -B build
cmake --build build -j4
sudo cmake --install build

# Lightweight alternative with minimal dependencies
# Compile only HRIT decoder, skip GUI (saves 500MB+)
cmake -DENABLE_GUI=OFF -DENABLE_HRIT=ON ..
```

**Configuration: satdump_config.ini**
```ini
[rtlsdr]
device_index = 0
sample_rate = 2400000      # 2.4 MSPS optimal for HRIT
frequency = 1694100000     # GOES-16/18 HRIT
gain = 30                  # Auto-adjust if needed
bias_tee = true            # Powers LNA

[demodulator]
mode = "hrit"              # or "lrit" for lower rate
output_type = "files"      # Save to disk

[processor]
# Auto-save images on successful decode
auto_save = true
save_dir = "/data/images"
metadata_json = true       # For indexing
```

**Alternative Legacy: goestools + xrit-rx**

For GK-2A (Asian LRIT) or legacy GOES compatibility:
```bash
# goesrecv: Demodulation/decoding
git clone https://github.com/pietern/goestools.git
cd goestools && make
# xrit-rx: LRIT packet processing
git clone https://github.com/vchistyakov/xrit-rx.git
```

#### Image Storage & Metadata Indexing

**Database Choice: SQLite (Local) + PostgreSQL (Optional Remote Sync)**

For Residential:
```sql
-- SQLite for fast local access, minimal overhead
CREATE TABLE images (
    id INTEGER PRIMARY KEY,
    filename TEXT UNIQUE,
    satellite TEXT,          -- GOES-16, GOES-18, GK-2A
    band INTEGER,            -- IR, VIS, WV
    timestamp DATETIME,
    resolution INTEGER,      -- 2km, 5km, etc
    size_bytes INTEGER,
    quality_score REAL,      -- 0-100 (Viterbi error rate)
    location_lat REAL,       -- station coordinates
    location_lon REAL,
    has_timeseries INTEGER,  -- linked to timelapse
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    indexed_at DATETIME
);

CREATE INDEX idx_timestamp ON images(timestamp);
CREATE INDEX idx_satellite ON images(satellite);
CREATE INDEX idx_band ON images(band);
CREATE VIRTUAL TABLE images_fts USING fts5(
    filename, satellite, band, content=images
);
```

For Portable/Remote Sync:
```sql
-- PostgreSQL on home server or cloud
-- Same schema as above, with replication support
-- Use pg_trgm extension for fuzzy image search
CREATE EXTENSION pg_trgm;
```

#### Time-Lapse Video Generation

**Tool: FFmpeg with optimized parameters**

```bash
#!/bin/bash
# generate_timelapse.sh
# Usage: ./generate_timelapse.sh /data/images/2026-01-06 output.mp4

INPUT_DIR=$1
OUTPUT_FILE=$2
FPS=${3:-10}        # Default 10 fps for weather

# Sort images chronologically
FILES=$(find "$INPUT_DIR" -name "*.png" -o -name "*.jpg" | sort)

# Generate timelapse with hardware acceleration if available
ffmpeg \
  -pattern_type glob -i "$INPUT_DIR/*.png" \
  -framerate $FPS \
  -c:v libx264 \
  -preset slow \
  -crf 23 \
  -pix_fmt yuv420p \
  -movflags +faststart \
  -metadata title="Weather Satellite Timelapse $(date)" \
  "$OUTPUT_FILE"

# H.265 for smaller file (if supported):
# -c:v libx265 -preset medium -crf 28

# Optimize for web streaming:
# -vf "scale=1920:1080" -maxrate 5M -bufsize 10M
```

**Performance Optimization:**
- Use libx265 (H.265) for 40-50% size reduction vs H.264
- Hardware acceleration: RPi 4 supports HEVC, NUC supports QuickSync
- Pre-process frames (resize to target resolution) before encoding

### 2.4 Heartbeat Service (System Health Monitoring)

**Purpose:** Detect station outages, track operational metrics

**Implementation: Python Service**

```python
#!/usr/bin/env python3
# heartbeat_service.py

import requests
import json
import os
import psutil
import subprocess
from datetime import datetime
from pathlib import Path
import time

class WeatherStationHeartbeat:
    def __init__(self, config_path="/etc/weather-station/heartbeat.conf"):
        self.config = self.load_config(config_path)
        self.station_id = self.config.get("station_id", "unknown")
        self.server_url = self.config.get("server_url")
        self.interval = self.config.get("interval_seconds", 300)
        
    def collect_metrics(self):
        """Gather system and satellite reception metrics"""
        metrics = {
            "station_id": self.station_id,
            "timestamp": datetime.utcnow().isoformat(),
            "system": {
                "cpu_percent": psutil.cpu_percent(interval=1),
                "memory_percent": psutil.virtual_memory().percent,
                "disk_percent": psutil.disk_usage('/').percent,
                "uptime_seconds": time.time() - psutil.boot_time(),
            },
            "network": {
                "internet_connected": self.check_internet(),
                "bytes_received": self.get_rx_bytes(),
                "bytes_sent": self.get_tx_bytes(),
            },
            "satellite": {
                "last_image_age_seconds": self.get_last_image_age(),
                "images_today": self.count_images_today(),
                "decoder_status": self.check_decoder(),
                "signal_quality": self.get_signal_quality(),
            },
        }
        return metrics
    
    def send_heartbeat(self):
        """Send metrics to central server"""
        if not self.server_url:
            return  # Offline mode
        
        try:
            metrics = self.collect_metrics()
            response = requests.post(
                f"{self.server_url}/api/heartbeat",
                json=metrics,
                timeout=10,
                verify=True
            )
            response.raise_for_status()
            print(f"[{metrics['timestamp']}] Heartbeat OK: {response.status_code}")
        except requests.exceptions.RequestException as e:
            print(f"[{datetime.now().isoformat()}] Heartbeat failed: {e}")
    
    def check_internet(self):
        """Check connectivity with fallback servers"""
        servers = ["8.8.8.8:53", "1.1.1.1:53", "208.67.222.222:53"]
        for server in servers:
            try:
                result = subprocess.run(
                    ["timeout", "2", "nc", "-zv"] + server.split(":"),
                    capture_output=True,
                    timeout=3
                )
                if result.returncode == 0:
                    return True
            except:
                pass
        return False
    
    def get_last_image_age(self):
        """Minutes since last successful satellite image"""
        images_dir = Path("/data/images")
        if not images_dir.exists():
            return -1
        
        images = list(images_dir.glob("**/*.png"))
        if not images:
            return -1
        
        last_image = max(images, key=lambda p: p.stat().st_mtime)
        age_seconds = time.time() - last_image.stat().st_mtime
        return int(age_seconds)
    
    def run(self):
        """Main loop"""
        print(f"Heartbeat service started. Interval: {self.interval}s")
        while True:
            self.send_heartbeat()
            time.sleep(self.interval)

if __name__ == "__main__":
    heartbeat = WeatherStationHeartbeat()
    heartbeat.run()
```

**Systemd Service Setup:**

```ini
# /etc/systemd/system/weather-heartbeat.service
[Unit]
Description=Weather Station Heartbeat Service
After=network.target

[Service]
Type=simple
User=weather
ExecStart=/usr/local/bin/heartbeat_service.py
Restart=always
RestartSec=60

[Install]
WantedBy=multi-user.target
```

**Installation:**
```bash
sudo systemctl enable weather-heartbeat.service
sudo systemctl start weather-heartbeat.service
sudo systemctl status weather-heartbeat.service
```

---

## Part 3: Web Interface & Display

### 3.1 Web Architecture

**Stack:**
- Backend: Flask (Python, minimal overhead)
- Frontend: HTML5 + Vanilla JS (no heavy frameworks)
- Server: Gunicorn + Nginx (reverse proxy)
- Storage: Static files + SQLite/PostgreSQL

**Lightweight Flask Application**

```python
#!/usr/bin/env python3
# app.py - Weather satellite image web interface

from flask import Flask, render_template, jsonify, request, send_file
from pathlib import Path
import sqlite3
import json
from datetime import datetime, timedelta
import os

app = Flask(__name__)
app.config['JSON_SORT_KEYS'] = False

# Database
DB_PATH = "/data/satellite.db"
IMAGES_PATH = "/data/images"

def get_db():
    """SQLite connection with row factory"""
    conn = sqlite3.connect(DB_PATH)
    conn.row_factory = sqlite3.Row
    return conn

@app.route('/')
def index():
    """Main dashboard"""
    return render_template('index.html')

@app.route('/api/images/latest')
def get_latest_images():
    """Get most recent images for each satellite/band"""
    conn = get_db()
    cursor = conn.cursor()
    
    query = """
    SELECT satellite, band, filename, timestamp, quality_score
    FROM images
    WHERE timestamp > datetime('now', '-24 hours')
    ORDER BY satellite, band, timestamp DESC
    LIMIT 10
    """
    
    images = [dict(row) for row in cursor.execute(query)]
    conn.close()
    
    return jsonify(images)

@app.route('/api/images/search')
def search_images():
    """Search images by date range, satellite, band"""
    start_date = request.args.get('start', 
                  (datetime.now() - timedelta(days=1)).isoformat())
    end_date = request.args.get('end', datetime.now().isoformat())
    satellite = request.args.get('satellite', '%')
    band = request.args.get('band', '%')
    quality_min = request.args.get('quality_min', 0, type=float)
    
    conn = get_db()
    cursor = conn.cursor()
    
    query = """
    SELECT id, filename, satellite, band, timestamp, quality_score
    FROM images
    WHERE timestamp BETWEEN ? AND ?
      AND satellite LIKE ?
      AND band LIKE ?
      AND quality_score >= ?
    ORDER BY timestamp DESC
    LIMIT 100
    """
    
    results = [dict(row) for row in cursor.execute(
        query, (start_date, end_date, satellite, band, quality_min)
    )]
    conn.close()
    
    return jsonify(results)

@app.route('/api/images/<int:image_id>')
def get_image(image_id):
    """Get full image metadata and thumbnail"""
    conn = get_db()
    cursor = conn.cursor()
    
    cursor.execute(
        "SELECT * FROM images WHERE id = ?", (image_id,)
    )
    image = dict(cursor.fetchone() or {})
    conn.close()
    
    if image:
        image['thumbnail_url'] = f"/thumbs/{image['filename']}"
        image['full_url'] = f"/images/{image['filename']}"
    
    return jsonify(image)

@app.route('/api/timelapse/generate', methods=['POST'])
def generate_timelapse():
    """Trigger timelapse generation for date range"""
    data = request.json
    start_date = data.get('start_date')
    end_date = data.get('end_date')
    satellite = data.get('satellite', 'GOES-16')
    band = data.get('band', '13')  # IR band
    fps = data.get('fps', 10)
    
    # Background task (simplified)
    job_id = datetime.now().timestamp()
    
    # Would typically queue to background worker
    # For now, just trigger generation
    return jsonify({
        'job_id': job_id,
        'status': 'queued',
        'output_file': f'timelapse_{job_id}.mp4'
    })

@app.route('/api/stats')
def get_stats():
    """System and reception statistics"""
    conn = get_db()
    cursor = conn.cursor()
    
    # Count images by satellite
    cursor.execute("""
        SELECT satellite, band, COUNT(*) as count,
               AVG(quality_score) as avg_quality
        FROM images
        WHERE timestamp > datetime('now', '-7 days')
        GROUP BY satellite, band
    """)
    stats = {row[0]: {row[1]: dict(count=row[2], avg_quality=row[3])} 
             for row in cursor.fetchall()}
    
    conn.close()
    return jsonify(stats)

@app.route('/api/status')
def get_status():
    """Real-time system status"""
    import psutil
    
    return jsonify({
        'timestamp': datetime.utcnow().isoformat(),
        'system': {
            'cpu_percent': psutil.cpu_percent(),
            'memory_percent': psutil.virtual_memory().percent,
            'disk_percent': psutil.disk_usage('/').percent,
        },
        'uptime_hours': int(
            (datetime.now() - datetime.fromtimestamp(
                os.path.getmtime(DB_PATH)
            )).total_seconds() / 3600
        )
    })

if __name__ == '__main__':
    # Development only, use Gunicorn in production
    app.run(host='0.0.0.0', port=5000, debug=False)
```

### 3.2 HTML Frontend

**Responsive, minimal HTML/CSS**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather Satellite Receiver</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
            background: #0f172a;
            color: #e2e8f0;
            line-height: 1.6;
        }
        
        header {
            background: linear-gradient(135deg, #1e3a8a 0%, #1e293b 100%);
            padding: 1rem 2rem;
            border-bottom: 2px solid #3b82f6;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 2rem;
        }
        
        h1 { font-size: 2rem; margin-bottom: 0.5rem; }
        
        .filters {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 1rem;
            margin-bottom: 2rem;
            padding: 1.5rem;
            background: #1e293b;
            border-radius: 8px;
        }
        
        .filter-group {
            display: flex;
            flex-direction: column;
        }
        
        label {
            font-weight: 600;
            margin-bottom: 0.5rem;
            font-size: 0.9rem;
        }
        
        input, select {
            padding: 0.5rem;
            border: 1px solid #475569;
            border-radius: 4px;
            background: #0f172a;
            color: #e2e8f0;
            font-size: 1rem;
        }
        
        button {
            padding: 0.75rem 1.5rem;
            background: #3b82f6;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-weight: 600;
            transition: background 0.3s;
        }
        
        button:hover {
            background: #2563eb;
        }
        
        .image-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 1.5rem;
            margin-top: 2rem;
        }
        
        .image-card {
            background: #1e293b;
            border-radius: 8px;
            overflow: hidden;
            border: 1px solid #475569;
            transition: transform 0.2s, box-shadow 0.2s;
        }
        
        .image-card:hover {
            transform: translateY(-4px);
            box-shadow: 0 4px 12px rgba(59, 130, 246, 0.2);
        }
        
        .image-card img {
            width: 100%;
            height: 200px;
            object-fit: cover;
            background: #0f172a;
        }
        
        .image-meta {
            padding: 1rem;
        }
        
        .image-meta p {
            margin-bottom: 0.3rem;
            font-size: 0.9rem;
            color: #cbd5e1;
        }
        
        .quality-badge {
            display: inline-block;
            padding: 0.25rem 0.75rem;
            border-radius: 3px;
            font-size: 0.85rem;
            font-weight: 600;
        }
        
        .quality-good { background: #10b981; color: white; }
        .quality-fair { background: #f59e0b; color: white; }
        .quality-poor { background: #ef4444; color: white; }
        
        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 1rem;
            margin-bottom: 2rem;
        }
        
        .stat-card {
            background: #1e293b;
            padding: 1.5rem;
            border-radius: 8px;
            border-left: 4px solid #3b82f6;
        }
        
        .stat-label {
            font-size: 0.9rem;
            color: #94a3b8;
            margin-bottom: 0.5rem;
        }
        
        .stat-value {
            font-size: 1.8rem;
            font-weight: bold;
            color: #3b82f6;
        }
        
        .loading {
            text-align: center;
            padding: 2rem;
            color: #94a3b8;
        }
        
        @media (max-width: 768px) {
            .container { padding: 1rem; }
            .image-grid { grid-template-columns: 1fr; }
            h1 { font-size: 1.5rem; }
        }
    </style>
</head>
<body>
    <header>
        <h1>🛰️ Weather Satellite Receiver</h1>
        <p>Real-time GEO satellite imagery capture & analysis</p>
    </header>
    
    <div class="container">
        <!-- System Status -->
        <div class="stats" id="status-container">
            <div class="loading">Loading system status...</div>
        </div>
        
        <!-- Search Filters -->
        <div class="filters">
            <div class="filter-group">
                <label for="satellite">Satellite</label>
                <select id="satellite">
                    <option value="">All Satellites</option>
                    <option value="GOES-16">GOES-16 (NOAA East)</option>
                    <option value="GOES-18">GOES-18 (NOAA West)</option>
                    <option value="GK-2A">GK-2A (Korea)</option>
                </select>
            </div>
            
            <div class="filter-group">
                <label for="band">Band</label>
                <select id="band">
                    <option value="">All Bands</option>
                    <option value="2">Band 2 (Visible)</option>
                    <option value="7">Band 7 (Short IR)</option>
                    <option value="13">Band 13 (Long IR)</option>
                </select>
            </div>
            
            <div class="filter-group">
                <label for="quality">Minimum Quality</label>
                <select id="quality">
                    <option value="0">All</option>
                    <option value="70">Good (70+)</option>
                    <option value="85">Excellent (85+)</option>
                </select>
            </div>
            
            <div class="filter-group">
                <label for="date-range">Date Range</label>
                <input type="date" id="start-date">
                <input type="date" id="end-date">
            </div>
            
            <button onclick="searchImages()">Search</button>
            <button onclick="generateTimelapse()">Generate Timelapse</button>
        </div>
        
        <!-- Image Gallery -->
        <div id="images-container" class="image-grid">
            <div class="loading">Loading images...</div>
        </div>
    </div>
    
    <script>
        // Load latest images on page load
        document.addEventListener('DOMContentLoaded', () => {
            loadLatestImages();
            loadSystemStatus();
            setInterval(loadSystemStatus, 30000);  // Update every 30s
        });
        
        async function loadLatestImages() {
            try {
                const resp = await fetch('/api/images/latest');
                const images = await resp.json();
                displayImages(images);
            } catch (err) {
                console.error('Failed to load images:', err);
            }
        }
        
        async function searchImages() {
            const satellite = document.getElementById('satellite').value;
            const band = document.getElementById('band').value;
            const quality = document.getElementById('quality').value;
            const startDate = document.getElementById('start-date').value;
            const endDate = document.getElementById('end-date').value;
            
            const params = new URLSearchParams({
                satellite: satellite || '%',
                band: band || '%',
                quality_min: quality || 0,
                start: startDate,
                end: endDate
            });
            
            try {
                const resp = await fetch(`/api/images/search?${params}`);
                const images = await resp.json();
                displayImages(images);
            } catch (err) {
                console.error('Search failed:', err);
            }
        }
        
        function displayImages(images) {
            const container = document.getElementById('images-container');
            
            if (!images || images.length === 0) {
                container.innerHTML = '<div class="loading">No images found</div>';
                return;
            }
            
            container.innerHTML = images.map(img => `
                <div class="image-card">
                    <img src="/thumbs/${img.filename}" alt="${img.satellite} ${img.band}">
                    <div class="image-meta">
                        <p><strong>${img.satellite}</strong> Band ${img.band}</p>
                        <p>${new Date(img.timestamp).toLocaleString()}</p>
                        <p class="quality-badge ${getQualityClass(img.quality_score)}">
                            Quality: ${Math.round(img.quality_score)}%
                        </p>
                    </div>
                </div>
            `).join('');
        }
        
        function getQualityClass(score) {
            if (score >= 85) return 'quality-good';
            if (score >= 70) return 'quality-fair';
            return 'quality-poor';
        }
        
        async function loadSystemStatus() {
            try {
                const resp = await fetch('/api/status');
                const status = await resp.json();
                updateStatusDisplay(status);
            } catch (err) {
                console.error('Failed to load status:', err);
            }
        }
        
        function updateStatusDisplay(status) {
            const container = document.getElementById('status-container');
            container.innerHTML = `
                <div class="stat-card">
                    <div class="stat-label">CPU Usage</div>
                    <div class="stat-value">${status.system.cpu_percent}%</div>
                </div>
                <div class="stat-card">
                    <div class="stat-label">Memory Usage</div>
                    <div class="stat-value">${status.system.memory_percent}%</div>
                </div>
                <div class="stat-card">
                    <div class="stat-label">Disk Usage</div>
                    <div class="stat-value">${status.system.disk_percent}%</div>
                </div>
                <div class="stat-card">
                    <div class="stat-label">Uptime (Hours)</div>
                    <div class="stat-value">${status.uptime_hours}</div>
                </div>
            `;
        }
        
        async function generateTimelapse() {
            const satellite = document.getElementById('satellite').value || 'GOES-16';
            const startDate = document.getElementById('start-date').value;
            const endDate = document.getElementById('end-date').value;
            
            if (!startDate || !endDate) {
                alert('Please select date range');
                return;
            }
            
            try {
                const resp = await fetch('/api/timelapse/generate', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({
                        satellite,
                        start_date: startDate,
                        end_date: endDate,
                        fps: 10
                    })
                });
                
                const result = await resp.json();
                alert(`Timelapse job queued: ${result.job_id}`);
            } catch (err) {
                console.error('Timelapse generation failed:', err);
                alert('Failed to generate timelapse');
            }
        }
    </script>
</body>
</html>
```

### 3.3 Web Server Configuration

**Nginx Reverse Proxy (Production)**

```nginx
# /etc/nginx/sites-available/weather-satellite

upstream flask_app {
    server 127.0.0.1:5000;
}

server {
    listen 80;
    server_name weather.local;  # or your domain
    
    # Redirect HTTP to HTTPS (if using SSL)
    # return 301 https://$server_name$request_uri;
    
    location / {
        proxy_pass http://flask_app;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_buffering off;
    }
    
    # Static files
    location /images/ {
        alias /data/images/;
        expires 7d;
        add_header Cache-Control "public, immutable";
    }
    
    location /thumbs/ {
        alias /data/thumbnails/;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
    
    # Performance
    client_max_body_size 100M;
    gzip on;
    gzip_types text/plain application/json text/javascript;
}
```

**Gunicorn Service**

```ini
# /etc/systemd/system/weather-web.service
[Unit]
Description=Weather Satellite Web Interface
After=network.target

[Service]
Type=notify
User=weather
WorkingDirectory=/opt/weather-station
ExecStart=/usr/bin/gunicorn \
    --workers 2 \
    --worker-class sync \
    --bind 127.0.0.1:5000 \
    --timeout 60 \
    app:app
Restart=always

[Install]
WantedBy=multi-user.target
```

---

## Part 4: OS, Software & Installation

### 4.1 Complete Installation Script

```bash
#!/bin/bash
# install_weather_satellite.sh
# Complete system setup for weather satellite receiver

set -e

echo "================================"
echo "Weather Satellite Receiver Setup"
echo "================================"

# Update system
echo "[1/8] Updating system packages..."
sudo apt update
sudo apt dist-upgrade -y

# Install dependencies
echo "[2/8] Installing dependencies..."
sudo apt install -y \
    git curl wget \
    build-essential cmake \
    libusb-1.0-0-dev \
    python3-pip python3-dev \
    ffmpeg \
    sqlite3 \
    nginx \
    postgresql postgresql-client \
    ufw

# Create weather user and directories
echo "[3/8] Creating system user and directories..."
sudo useradd -m -s /bin/bash weather || true
sudo mkdir -p /data/{images,thumbnails,logs}
sudo chown -R weather:weather /data

# Clone and install SATDUMP
echo "[4/8] Installing SATDUMP..."
cd /opt
sudo git clone https://github.com/altillimity/SatDump.git
cd SatDump
sudo mkdir -p build
cd build
sudo cmake -DENABLE_GUI=OFF -DCMAKE_BUILD_TYPE=Release ..
sudo cmake --build . -j4
sudo cmake --install .

# Install Python dependencies
echo "[5/8] Installing Python packages..."
sudo pip3 install -r requirements.txt
# Or manually:
# sudo pip3 install flask gunicorn psutil requests

# Configure RTL-SDR permissions
echo "[6/8] Configuring RTL-SDR access..."
echo 'SUBSYSTEMS=="usb", ATTRS{idVendor}=="0bda", ATTRS{idProduct}=="2838", GROUP="weather"' | \
    sudo tee /etc/udev/rules.d/20-rtl-sdr.rules
sudo udevadm control --reload-rules

# Setup firewall
echo "[7/8] Configuring firewall..."
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp     # SSH
sudo ufw allow 80/tcp     # HTTP
sudo ufw allow 443/tcp    # HTTPS
sudo ufw enable

# Enable services
echo "[8/8] Enabling services..."
sudo systemctl enable nginx
sudo systemctl enable weather-web.service
sudo systemctl enable weather-receiver.service
sudo systemctl enable weather-heartbeat.service

echo ""
echo "================================"
echo "Installation Complete!"
echo "================================"
echo ""
echo "Next steps:"
echo "1. Configure satellite receiver: sudo nano /etc/weather-station/satdump.conf"
echo "2. Configure heartbeat: sudo nano /etc/weather-station/heartbeat.conf"
echo "3. Start services: sudo systemctl start weather-receiver"
echo "4. Access web interface: http://localhost"
echo ""
```

### 4.2 Configuration Files

**Satellite Receiver Configuration**

```ini
# /etc/weather-station/satdump.conf

[general]
station_id=weather_station_01
station_location=City, Country
latitude=45.5
longitude=13.6

[rtlsdr]
device_index=0
sample_rate=2400000
frequency=1694100000
gain=30
bias_tee=true

[demodulation]
mode=hrit
timeout_minutes=5
save_raw_data=false

[processing]
auto_save=true
save_directory=/data/images
generate_thumbnails=true
thumbnail_size=300x300
metadata_format=json

[database]
type=sqlite
path=/data/satellite.db
enable_fts=true

[logging]
level=INFO
file=/data/logs/satdump.log
max_file_size_mb=100
max_backups=10
```

**Heartbeat Configuration**

```ini
# /etc/weather-station/heartbeat.conf

[general]
station_id=weather_station_01
interval_seconds=300

[server]
# Leave empty for offline mode
url=https://weather-monitoring.example.com

[network]
check_internet=true
timeout_seconds=10

[thresholds]
cpu_warning_percent=80
memory_warning_percent=80
disk_warning_percent=90
image_age_warning_minutes=60
```

---

## Part 5: Integration & Deployment

### 5.1 Complete Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    COMPLETE DATA FLOW                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Satellite Signal (1694 MHz)                                     │
│         ↓                                                       │
│    Antenna & LNA                                                │
│         ↓                                                       │
│    RTL-SDR Dongle (raw IQ data 2.4 MSPS)                        │
│         ↓                                                       │
│  SATDUMP Demodulator                                            │
│  ├─ BPSK demodulation                                           │
│  ├─ Viterbi error correction                                    │
│  ├─ Sync detection                                              │
│         ↓                                                       │
│  HRIT Packet Decoder (goesrecv/xrit-rx OR SatDump native)       │
│  ├─ Packet extraction                                           │
│  ├─ Image reconstruction                                        │
│  ├─ Metadata extraction                                         │
│         ↓                                                       │
│  Image File (PNG/TIFF/NetCDF)                                   │
│  └─ Timestamp + Quality Metrics                                 │
│         ↓                                                       │
│  ┌──────────────────────────────────────────┐                   │
│  │ Database Insertion (SQLite/PostgreSQL)   │                   │
│  │ ├─ Filename, timestamp, satellite        │                   │
│  │ ├─ Band, resolution, quality_score       │                   │
│  │ └─ FTS indexing for search               │                   │
│  └──────────────────────────────────────────┘                   │
│         ↓                                                       │
│  Thumbnail Generation (300x300 px)                              │
│  └─ Stored in /data/thumbnails/                                 │
│         ↓                                                       │
│  Web Interface (Flask)                                          │
│  ├─ API: /api/images/latest                                     │
│  ├─ API: /api/images/search                                     │
│  ├─ API: /api/timelapse/generate                                │
│  └─ Dashboard: HTML5 responsive display                         │
│         ↓                                                       │
│  Time-Lapse Generation (Nightly)                                │
│  └─ FFmpeg video encoding (MP4/HEVC)                            │
│         ↓                                                       │
│  Heartbeat Service (Every 5 minutes)                            │
│  ├─ System metrics (CPU, memory, disk)                          │
│  ├─ Network status                                              │
│  ├─ Satellite reception quality                                 │
│  └─ POST to central server (if connected)                       │
│         ↓                                                       │
│  Data Storage & Archive                                         │
│  ├─ Fast access: /data/images/ (current week)                   │
│  ├─ Archive: /data/archive/ (older data)                        │
│  └─ Backup: External USB or cloud (optional)                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 5.2 Systemd Services

**Receiver Service** (runs continuous demodulation)

```ini
# /etc/systemd/system/weather-receiver.service
[Unit]
Description=Weather Satellite HRIT Receiver
After=network.target

[Service]
Type=simple
User=weather
WorkingDirectory=/opt/SatDump
ExecStart=/usr/local/bin/satdump live goes_hrit rtlsdr \
    --samplerate 2400000 \
    --frequency 1694100000 \
    --gain 30 \
    --output /data/images
Restart=always
RestartSec=10

StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

**Processing Service** (handles image post-processing)

```ini
# /etc/systemd/system/weather-processor.service
[Unit]
Description=Weather Image Processor
After=weather-receiver.service

[Service]
Type=simple
User=weather
ExecStart=/opt/weather-station/image_processor.py
Restart=always
RestartSec=30

[Install]
WantedBy=multi-user.target
```

**Image Processor Script**

```python
#!/usr/bin/env python3
# image_processor.py
# Handles thumbnails, metadata, and timelapse generation

import sqlite3
import os
from pathlib import Path
from PIL import Image
import json
import time
from datetime import datetime
import subprocess
import logging

logging.basicConfig(
    filename='/data/logs/processor.log',
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

IMAGES_DIR = Path('/data/images')
THUMBS_DIR = Path('/data/thumbnails')
DB_PATH = '/data/satellite.db'

def process_new_images():
    """Scan for new images and create thumbnails, metadata"""
    
    # Find all PNG/TIFF files without corresponding database entry
    conn = sqlite3.connect(DB_PATH)
    cursor = conn.cursor()
    
    cursor.execute("SELECT filename FROM images")
    indexed = {row[0] for row in cursor.fetchall()}
    
    for image_path in IMAGES_DIR.glob('**/*.png'):
        if image_path.name not in indexed:
            try:
                process_image(image_path, cursor, conn)
                logging.info(f"Processed: {image_path.name}")
            except Exception as e:
                logging.error(f"Error processing {image_path}: {e}")
    
    conn.commit()
    conn.close()

def process_image(image_path, cursor, conn):
    """Extract metadata and create thumbnail"""
    
    # Get file stats
    stat = image_path.stat()
    mtime = datetime.fromtimestamp(stat.st_mtime)
    
    # Parse metadata from filename if available
    # Format: GOES-16_CH13_2026-01-06_120000.png
    parts = image_path.stem.split('_')
    
    satellite = parts[0] if len(parts) > 0 else 'UNKNOWN'
    channel = parts[1] if len(parts) > 1 else 'CH13'
    band = int(channel[2:]) if channel.startswith('CH') else 13
    
    # Create thumbnail
    create_thumbnail(image_path)
    
    # Calculate quality score (Viterbi correction rate if available)
    # For now, default to 85 (good quality)
    quality_score = 85.0
    
    # Insert into database
    cursor.execute("""
        INSERT INTO images (
            filename, satellite, band, timestamp, 
            resolution, size_bytes, quality_score, indexed_at
        ) VALUES (?, ?, ?, ?, ?, ?, ?, ?)
    """, (
        image_path.name,
        satellite,
        band,
        mtime.isoformat(),
        2000,  # 2km resolution default
        stat.st_size,
        quality_score,
        datetime.utcnow().isoformat()
    ))

def create_thumbnail(image_path, size=(300, 300)):
    """Generate thumbnail image"""
    
    try:
        img = Image.open(image_path)
        img.thumbnail(size, Image.Resampling.LANCZOS)
        
        thumb_path = THUMBS_DIR / (image_path.stem + '_thumb.png')
        img.save(thumb_path, quality=85)
        
    except Exception as e:
        logging.error(f"Thumbnail error for {image_path}: {e}")

def generate_daily_timelapse():
    """Create timelapse from yesterday's images"""
    
    yesterday = (datetime.now() - timedelta(days=1)).date()
    pattern = f"{yesterday}*.png"
    
    images = list(IMAGES_DIR.glob(pattern))
    if not images:
        return
    
    output_file = f"/data/videos/timelapse_{yesterday}.mp4"
    
    # Use FFmpeg to create timelapse
    cmd = [
        'ffmpeg',
        '-pattern_type', 'glob',
        '-i', str(IMAGES_DIR / pattern),
        '-framerate', '10',
        '-c:v', 'libx265',
        '-preset', 'medium',
        '-crf', '28',
        '-pix_fmt', 'yuv420p',
        output_file
    ]
    
    try:
        subprocess.run(cmd, check=True, capture_output=True)
        logging.info(f"Generated timelapse: {output_file}")
    except Exception as e:
        logging.error(f"Timelapse generation failed: {e}")

if __name__ == '__main__':
    THUMBS_DIR.mkdir(exist_ok=True)
    
    # Run continuously
    while True:
        process_new_images()
        
        # Generate daily timelapse at midnight
        if datetime.now().hour == 0 and datetime.now().minute < 5:
            generate_daily_timelapse()
        
        time.sleep(60)  # Check every minute
```

---

## Part 6: Development Workflow & GitHub Setup

### 6.1 GitHub Repository Structure

```
weather-satellite-receiver/
├── README.md
├── LICENSE (MIT recommended)
├── .gitignore
├── .github/
│   └── workflows/
│       ├── ci.yml (automated testing)
│       └── docs-build.yml
├── docs/
│   ├── INSTALLATION.md
│   ├── CONFIGURATION.md
│   ├── HARDWARE.md
│   ├── API.md
│   └── TROUBLESHOOTING.md
├── hardware/
│   ├── antenna_design/
│   │   └── parabolic_dish_notes.md
│   └── parts_list.csv
├── software/
│   ├── receiver/
│   │   ├── receiver_service.py
│   │   ├── satdump.conf.example
│   │   └── Makefile
│   ├── processor/
│   │   ├── image_processor.py
│   │   ├── database.sql
│   │   └── requirements.txt
│   ├── web/
│   │   ├── app.py (Flask application)
│   │   ├── requirements.txt
│   │   ├── templates/
│   │   │   └── index.html
│   │   ├── static/
│   │   │   ├── css/
│   │   │   ├── js/
│   │   │   └── images/
│   │   └── nginx.conf
│   ├── heartbeat/
│   │   ├── heartbeat_service.py
│   │   └── heartbeat.conf.example
│   └── utils/
│       ├── setup.sh
│       ├── backup.sh
│       └── diagnostics.sh
├── tests/
│   ├── test_receiver.py
│   ├── test_database.py
│   └── test_web_api.py
├── logs/
│   └── CHANGELOG.md
└── docker/
    ├── Dockerfile
    └── docker-compose.yml
```

### 6.2 Development Workflow

**1. Create GitHub repository:**
```bash
mkdir weather-satellite-receiver
cd weather-satellite-receiver
git init
git add .
git commit -m "Initial commit: Project structure and planning"
git remote add origin https://github.com/YOUR_USERNAME/weather-satellite-receiver.git
git push -u origin main
```

**2. Development branches:**
```bash
# Feature development
git checkout -b feature/polarorbiter-support
# Make changes
git commit -m "Add polar orbiter reception support"
git push origin feature/polarorbiter-support
# Create PR on GitHub for review

# Bugfix
git checkout -b fix/decoder-crash
# Fix and commit
git push origin fix/decoder-crash
```

**3. Version management (Semantic Versioning):**
```bash
# Release version
git tag -a v0.1.0 -m "Initial release: GOES HRIT reception"
git push origin v0.1.0

# Changelog entry in CHANGELOG.md:
# ## [0.1.0] - 2026-01-06
# ### Added
# - HRIT GOES-16/18 reception
# - Web interface for image browsing
# - Heartbeat monitoring service
# ### Fixed
# - RTL-SDR initialization issues
```

### 6.3 Development Logbook Template

```markdown
# Development Logbook

## 2026-01-06

### Session 1: Hardware Assembly
- **Duration:** 3 hours
- **Tasks Completed:**
  - ✅ Assembled parabolic dish antenna
  - ✅ Installed LNA (SAWbird+)
  - ✅ Tested cable connections
  - ✅ Verified RTL-SDR power consumption
- **Issues Encountered:**
  - Cable connector slightly loose, retightened
- **Commits:**
  - `Hardware assembly completed, cable lengths documented`

### Session 2: Initial Reception Test
- **Duration:** 2 hours
- **Tasks Completed:**
  - ✅ Configured RTL-SDR frequency
  - ✅ First successful GOES-16 signal lock
  - ✅ Captured 10 frames with good signal
- **Issues Encountered:**
  - Gain adjustment needed (set to 28 dB optimal)
- **Next Steps:**
  - Set up web interface
  - Test 24-hour continuous operation
- **Commits:**
  - `First successful GOES-16 HRIT reception`

## [Continue daily entries...]
```

---

## Part 7: Optional Enhancements

### 7.1 Polar Orbiter Reception (137 MHz)

**Additional Components:**
- QFH antenna (137 MHz, omnidirectional)
- Separate RTL-SDR dongle (or dual tuner)
- LNA for 137 MHz (~$40)

**Software Support:**
- NOAA-18/19 reception via `WXtoImg` or `goesrecv` with polar support
- Automatic satellite pass prediction using Gpredict
- Image overlaying: Polar orbiter on GEO background

**Implementation:**
```python
# Auto-track and receive NOAA passes
import ephem
import subprocess
from datetime import datetime

observer = ephem.Observer()
observer.lat = '45.5'
observer.lon = '13.6'

noaa18 = ephem.readtle(
    'NOAA-18',
    '1 28654U 05018A   26001.50000000  .00000357  00000-0  ...',
    '2 28654  99.0090 337.8869 0014743 243.6885  78.7231 14.12502858913210'
)

for rise, peak, setting in [ephem.next_pass(observer, noaa18) 
                              for _ in range(5)]:
    if peak.alt > 0.3:  # >15° elevation
        # Start receiver 30 seconds before pass
        subprocess.Popen([
            'satdump', 'live', 'noaa_apt',
            'rtlsdr', '--duration', str(int((setting - rise) * 86400))
        ])
```

### 7.2 GPS Module Integration

**Hardware:**
- NEO-6M GPS module ($15 USD)
- Connected via UART to Raspberry Pi

**Benefits:**
- Precise station location
- Automatic time synchronization
- Satellite azimuth/elevation tracking
- Multi-station coordinate for image fusion

```python
# GPS integration for precise positioning
import serial
import pynmea2

gps_serial = serial.Serial('/dev/ttyAMA0', baudrate=9600)

while True:
    line = gps_serial.readline().decode('utf-8', errors='ignore')
    try:
        msg = pynmea2.parse(line)
        if isinstance(msg, pynmea2.GGA):
            latitude = msg.latitude
            longitude = msg.longitude
            altitude = msg.altitude
            # Update station coordinates in database
    except:
        pass
```

### 7.3 Multi-Station Federation (360° Coverage)

**Concept:** Network multiple stations for global coverage

```python
# Central server aggregation
@app.route('/api/federate/upload', methods=['POST'])
def receive_station_data():
    """Receive images and metadata from remote stations"""
    data = request.json
    
    # Verify station signature
    if not verify_station_signature(data):
        return {'error': 'Invalid signature'}, 401
    
    # Store remote image with origin attribution
    image = {
        'filename': data['filename'],
        'satellite': data['satellite'],
        'timestamp': data['timestamp'],
        'station_id': data['station_id'],
        'coverage_area': data.get('geographic_bounds'),
        'quality': data['quality_score']
    }
    
    store_federated_image(image)
    
    return {'status': 'accepted', 'id': image['id']}

# Web interface overlay: Show coverage from all stations
# Combine images from multiple sources for wider geographic view
```

### 7.4 AI-Powered Event Detection

**Integration:**
- Real-time processing of satellite images for weather events
- Hurricane detection, convection tracking
- Automated alerts

```python
# Lightweight CNN for event detection
import torch
import torchvision.models as models

model = models.mobilenet_v2(pretrained=True)
model.eval()

def detect_storm(image_path):
    """Detect storm signatures in satellite image"""
    img = Image.open(image_path)
    img_tensor = transforms.ToTensor()(img)
    
    with torch.no_grad():
        features = model(img_tensor.unsqueeze(0))
    
    # Analyze brightness, texture patterns
    # Return confidence score for storm presence
```

---

## Part 8: Regional Satellite Frequencies

### US (NOAA East/West)

| Satellite | Frequency | Downlink | Coverage |
|-----------|-----------|----------|----------|
| GOES-16 (East) | 1694.1 MHz HRIT 400 kbps | CONUS | Full disk every 15 min |
| GOES-18 (West) | 1694.1 MHz HRIT 400 kbps | Pacific | Full disk every 15 min |
| GOES-15 (Legacy) | 1687.0 MHz LRIT | - | Deactivated May 2019 |

### Europe (Meteosat)

| Satellite | Frequency | Downlink | Coverage |
|-----------|-----------|----------|----------|
| Meteosat 8 (MSG-1) | 10701-10800 MHz | HRIT | Europe/Africa |
| Meteosat 9 (MSG-2) | 10701-10800 MHz | HRI-LRIT | Europe/Africa |
| Meteosat 11 (MSG-4) | 10701-10800 MHz | MTG bands | Europe |

**Reception note:** Requires parabolic dish >1.8m and more powerful receiver

### Asia-Pacific

| Satellite | Frequency | Downlink | Coverage |
|-----------|-----------|----------|----------|
| GK-2A (S. Korea) | 1692.14 MHz LRIT 64 kbps | Every 10 min | East Asia/Pacific |
| Himawari-8 (Japan) | 1707-1790 MHz | LRIT/HRIT | Western Pacific |
| Himawari-9 | 1707-1790 MHz | HRIT | Asia/Pacific |

### Africa/Middle East

| Satellite | Frequency | Downlink | Coverage |
|-----------|-----------|----------|----------|
| Meteosat 8/11 | 10701-10800 MHz | HRIT | Africa/Europe/Middle East |
| INSAT-3D (India) | 1692 MHz | INSAT downlink | Indian subcontinent |

### Australia

| Satellite | Frequency | Downlink | Coverage |
|-----------|-----------|----------|----------|
| Himawari-8/9 | 1707-1790 MHz | LRIT/HRIT | Australia/Pacific |

---

## Part 9: Bill of Materials & Cost Estimates

### Residential Fixed System (GOES-16/18 + GK-2A)

| Component | Part # / Model | Cost USD | Notes |
|-----------|---|------|-------|
| **Antenna** | | | |
| Parabolic Dish (2.4 GHz) | Generic WiFi surplus | $50-100 | 60-90cm |
| LNA (SAWbird+ GOES) | NooElec SAWbird+ | $75 | Filtered 1.6-1.8 GHz |
| Bandpass Filter | Generic 1.7 GHz | $15 | Optional, 2dB loss |
| Antenna Mount | Aluminum roofpole | $100 | Weatherproof |
| Cable (LMR400) | 10m spool | $50 | Low-loss |
| Connectors/adapters | SMA/N | $20 | Various |
| **Receiver** | | | |
| RTL-SDR (NESDR XTR) | NooElec NESDR SMArTee | $40 | USB dongle |
| USB Hub | Powered USB 3.0 | $30 | Backup storage |
| **Compute** | | | |
| Raspberry Pi 4 | 8GB RAM | $75 | Single board |
| MicroSD Card | Samsung Pro Plus 128GB | $25 | Fast card |
| Heatsink + Fan | Passive/fanless | $15 | Cooling |
| Power Supply | 5V 3.5A USB-C | $20 | Quality PSU |
| External SSD | 1TB USB 3.0 | $80 | Archive storage |
| **Networking** | | | |
| Ethernet Cable | Cat6A 30m | $30 | Wired preferred |
| WiFi Adapter | USB WiFi 5 | $20 | Backup |
| **Misc** | | | |
| Cables/hardware | Various | $50 | Adapters, tools |
| Case/housing | Pelican or weather box | $40 | Protection |
| | **TOTAL RESIDENTIAL** | **$685-750** | |

### Portable Field System

| Component | Part # | Cost USD | Notes |
|-----------|--------|------|-------|
| **Antenna (Portable)** | | | |
| Parabolic Dish (folding) | Custom tripod mount | $80 | Compact setup |
| LNA (SAWbird+) | NooElec SAWbird+ | $75 | Same as fixed |
| Quick-disconnect cables | SMA/N | $30 | Easy setup |
| Tripod antenna mount | Professional | $60 | Sturdy |
| **Compute (Portable)** | | | |
| Intel NUC mini PC | NUC i5 | $500 | Or Raspberry Pi 4 |
| 512GB NVMe SSD | Samsung 970 EVO | $60 | OS/apps |
| 2TB HDD | Seagate Barracuda 2.5" | $70 | Image storage |
| 19V Auto PSU | Converter 12-24V | $40 | Car power |
| **Storage** | | | |
| Hard Carrying Case | Pelican 1650 | $150 | Transport |
| Cable organizers | Various | $20 | Neat cables |
| **Accessories** | | | |
| Mobile hotspot | 4G LTE dongle | $50 | Optional internet |
| Power bank | 25000mAh | $80 | Backup 12V |
| | **TOTAL PORTABLE** | **$1,145-1,200** | |

**Cost-Optimized Alternative (Residential):**
- Use Raspberry Pi 4 (reduce compute cost by $425)
- DIY QFH antenna for polar reception (reduce by $100)
- **Minimal system: $200-250 for GOES reception only**

---

## Conclusion & Next Steps

This comprehensive system provides:

✅ **Full GEO satellite reception** (GOES-16/18, GK-2A)  
✅ **Automatic image processing & storage**  
✅ **Intuitive web interface** for browsing & analysis  
✅ **Time-lapse video generation**  
✅ **System health monitoring** via heartbeat service  
✅ **Flexible deployment** (residential or portable)  
✅ **Extensible architecture** for enhancements  
✅ **Open-source, well-documented codebase**  

**Recommended First Steps:**

1. **Week 1:** Gather hardware, test antenna design
2. **Week 2:** Set up compute system, install OS
3. **Week 3:** Configure SDR receiver, test signal lock
4. **Week 4:** Deploy web interface, refine image processing
5. **Week 5+:** Optimize, add enhancements, document learnings

**Resources:**
- RTL-SDR.com comprehensive guides
- goestools GitHub: https://github.com/pietern/goestools
- SATDUMP project: https://github.com/altillimity/SatDump
- NOAA GOES-R documentation: https://www.goes-r.gov
- Raspberry Pi documentation: https://www.raspberrypi.com/documentation

---

**Version History:**
- v1.0 | 2026-01-06 | Initial comprehensive system plan


