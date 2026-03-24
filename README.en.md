# CEAC Visa Status Checker

Automatically check U.S. visa application status from CEAC (NIV), solve captcha with Zhipu Vision, and return structured JSON output.

> Default language: Chinese
>
> Language: [中文](README.md) | English

## Features

- Automatically opens the CEAC status page (NIV)
- Automatically fetches and solves captcha with Zhipu Vision
- Automatically submits the query and parses response fields
- Retries automatically on transient failures
- Standard JSON output for easy automation integration

## Project Structure

```text
.
├─ requirements.txt
├─ scripts/
│  └─ check_ceac_status.py
└─ references/
   └─ LOCATION.md
```

## Requirements

- Python 3.9+
- Network access to https://ceac.state.gov
- Zhipu API key for captcha OCR

## Install Dependencies

```bash
python -m pip install -r requirements.txt
```

## Configure Environment Variables

Copy .env.example.txt to .env and fill in real values.

```env
LOCATION=CHINA, BEIJING
NUMBER=AA0020AKAX
PASSPORT_NUMBER=E12345678
SURNAME=ZHANG
ZHIPU_API_KEY=your_zhipu_api_key

# Optional
ZHIPU_MODEL_VISION=glm-4v-flash
MAX_RETRIES=5
RETRY_DELAY_SECONDS=5
```

### Required Fields

- LOCATION: Location text shown in CEAC dropdown (for example CHINA, BEIJING)
- NUMBER: Application ID or Case Number
- PASSPORT_NUMBER: Passport number
- SURNAME: Surname letters exactly as used in CEAC
- ZHIPU_API_KEY: Zhipu API key

For location examples, see references/LOCATION.md.

## Run

```bash
python scripts/check_ceac_status.py
```

The script reads .env and prints JSON to stdout.

## Output Examples

Success:

```json
{
  "success": true,
  "time": "2026-03-24 10:12:30",
  "visa_type": "NON IMMIGRANT VISA",
  "status": "Approved",
  "case_created": "01-Jan-2026",
  "case_last_updated": "20-Mar-2026",
  "description": "Your visa has been approved...",
  "application_num": "AA0020AKAX",
  "application_num_origin": "AA0020AKAX",
  "location": "CHINA, BEIJING"
}
```

Failure:

```json
{
  "success": false,
  "error": "QUERY_FAILED",
  "message": "Failed after 5 attempts.",
  "last_error": "..."
}
```

On failure, the process exits with a non-zero code, which is useful for CI and schedulers.

## FAQ

### 1) LOCATION not matched

- Ensure LOCATION matches CEAC dropdown text
- Check references/LOCATION.md
- The script also tries fuzzy matching (case-insensitive, extra spaces ignored)

### 2) Occasional captcha OCR failure

- This can happen occasionally
- Increase MAX_RETRIES and RETRY_DELAY_SECONDS if needed

### 3) API key or network issues

- Verify ZHIPU_API_KEY is valid
- Ensure network access to CEAC and Zhipu endpoints

## Related Links

- Skill page: https://clawhub.ai/xavierjiezou/ceac-visa-status-checker
- Reference repository: https://github.com/Andision/CEACStatusBot
- Reference repository: https://github.com/dutou520/SakuraFRP_Auto_AI_check

## Security Notes

- Do not commit .env to version control
- Do not hardcode API keys, passport numbers, or other sensitive data

## Disclaimer

This project is for personal automation and learning only. Please follow CEAC terms of use and local laws/regulations.
