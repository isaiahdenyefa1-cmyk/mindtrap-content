# MindTrap Remote Content Repository

Official remote level-pack and content-distribution repository for **MindTrap**, the native Android brain-teaser and logic puzzle game created by **Isaiah Denyefa** ([IsaiahAIhub](https://t.me/isaiahdenailab)).

This repository serves versioned puzzle level packs and the primary distribution manifest over raw HTTPS to MindTrap Android clients.

---

## Repository Structure

```text
mindtrap-content/
├── .gitignore
├── README.md
├── content-manifest.json
└── packs/
    ├── pack_cryptic_logic.json
    ├── pack_grandmaster_logic.json
    ├── pack_lateral_horizons.json
    └── pack_quantum_memory.json
```

---

## Published Content Packs

| Pack ID | Pack Name | Arena Category | Levels | Tier | SHA-256 (Checksum) | Size (Bytes) |
| :--- | :--- | :--- | :---: | :---: | :--- | :---: |
| `pack_lateral_horizons` | Lateral Horizons | Creative Thinking | 5 | Free | `b068bff7ae31d2ef4b1f7ad7e0edc5e73c520ea4a7919cee2994989192baef93` | 5,591 |
| `pack_quantum_memory` | Quantum Matrix | Memory Matrix | 5 | Free | `9159967dc78098cec03942aadb3b5911a29f56299f8cb6852e7353c51dc2cb01` | 3,410 |
| `pack_cryptic_logic` | Cryptic Logic Expansion | Logic Traps | 5 | Free | `6151e2fff5099309797f7dd6658362d3d0a45d2e178f1c0b0e28c813f82a6347` | 6,019 |
| `pack_grandmaster_logic` | Grandmaster Paradoxes | Grandmaster Enigmas | 10 | Premium | `9774867def1e27807302d7bc0f44d0f8f11833bfd4e97b52bd6751e433b8b6d3` | 12,894 |

---

## Schemas & Validation Rules

MindTrap's native engine (`PackValidator.kt`) strictly verifies all downloaded content before installing to app-private storage:

### 1. `content-manifest.json` Specification
```json
{
  "manifestVersion": 1,
  "engineVersion": 1,
  "generatedAt": "2026-10-31T12:00:00Z",
  "packs": [
    {
      "id": "pack_id",
      "name": "Pack Name",
      "description": "Short description of the pack contents",
      "version": 1,
      "minEngineVersion": 1,
      "isPremium": false,
      "levelCount": 5,
      "downloadSize": 5591,
      "downloadUrl": "https://raw.githubusercontent.com/isaiahdenyefa1-cmyk/mindtrap-content/main/packs/pack_id.json",
      "sha256": "64_character_lowercase_hex_hash"
    }
  ]
}
```

### 2. Level Pack JSON (`packs/<pack_id>.json`) Specification
```json
{
  "packId": "pack_id",
  "name": "Pack Name",
  "version": 1,
  "minEngineVersion": 1,
  "isPremium": false,
  "levels": [
    {
      "id": "unique_level_id",
      "category": "LOGIC | MEMORY | OBSERVATION | CREATIVE",
      "title": "Level Title",
      "prompt": "Riddle or question prompt text",
      "options": ["Choice A", "Choice B", "Choice C", "Choice D"],
      "correctIndex": 0,
      "hint": "Clue provided to the player",
      "explanation": "Detailed solution shown after completion",
      "difficulty": "Easy | Medium | Hard | Master Mind",
      "memorySequence": [0, 4, 8] // Required only for MEMORY category (0-8 grid node indices)
    }
  ]
}
```

---

## Step-by-Step Publishing & Upload Instructions

### Step 1: Upload Order & Dependency
To prevent broken download links or validation failures when the Android app queries the manifest, adhere to the following sequence:

1. **Upload all pack files in `packs/` first**:
   - `packs/pack_lateral_horizons.json`
   - `packs/pack_quantum_memory.json`
   - `packs/pack_cryptic_logic.json`
   - `packs/pack_grandmaster_logic.json`
   *This ensures that raw URLs like `https://raw.githubusercontent.com/isaiahdenyefa1-cmyk/mindtrap-content/main/packs/pack_lateral_horizons.json` immediately resolve with HTTP 200.*

2. **Upload `content-manifest.json`**:
   - Contains the exact byte sizes and SHA-256 hashes of the uploaded packs.

3. **Upload `README.md` and `.gitignore`**.

*(Note: If pushing via `git`, you can stage and commit all files simultaneously in one commit, which atomically makes both the manifest and the pack files available).*

### Step 2: Uploading via GitHub Web Interface
1. Open your repository: [https://github.com/isaiahdenyefa1-cmyk/mindtrap-content](https://github.com/isaiahdenyefa1-cmyk/mindtrap-content).
2. Click **Add file** -> **Create new file**.
3. In the filename input, type `packs/pack_lateral_horizons.json` (typing the `/` creates the `packs` folder automatically).
4. Paste the complete file content and click **Commit changes**.
5. Repeat for the other 3 pack JSON files inside `packs/`.
6. Return to the repository root, create `content-manifest.json`, paste its content, and commit.
7. Create `README.md` in the repository root and commit.

### Step 3: Uploading via Git CLI
```bash
git clone https://github.com/isaiahdenyefa1-cmyk/mindtrap-content.git
cd mindtrap-content
mkdir -p packs

# Copy or create all pack files into packs/
# Copy or create content-manifest.json and README.md in root

git add .
git commit -m "feat: publish initial MindTrap content manifest and level packs"
git branch -M main
git push -u origin main
```

---

## How to Calculate SHA-256 and File Sizes for New Packs

When authoring new packs or updating existing ones:

### Linux / macOS Terminal:
```bash
sha256sum packs/pack_lateral_horizons.json
wc -c packs/pack_lateral_horizons.json
```

### Python:
```python
import hashlib

with open("packs/pack_lateral_horizons.json", "rb") as f:
    data = f.read()
    print("SHA-256:", hashlib.sha256(data).hexdigest())
    print("Size (bytes):", len(data))
```

Always ensure the `sha256` string in `content-manifest.json` is in lowercase hex (64 characters) and matches the exact byte count of the file.
