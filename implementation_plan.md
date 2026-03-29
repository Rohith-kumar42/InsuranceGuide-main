# Insurance Data Matching and Merging

Plan to integrate scraped insurance data from `insurance data new.docx` into existing insurance records in the Node.js/React codebase.

## Proposed Changes

### Data Integration Script [NEW]
- Create `backend/src/utils/mergeScrapedData.js` to handle the matching and merging logic.
- The script will:
  1. Parse the scraped data from the docx file (using a helper or the existing [read_docx.py](file:///c:/Users/Balraj/Downloads/InsuranceGuide-main/read_docx.py) output).
  2. Load existing insurance records from the database using the `InsuranceProduct` model.
  3. Match scraped entries using the defined priority:
     - **Exact Match**: `product_uin` / `policy_id`.
     - **Fuzzy Match**: `product_name` + `insurer_name`.
     - **Ambiguous Match**: `insurance_type` + `insurer_name`.
  4. Merge fields into existing records:
     - Only add **new** fields or **missing** details.
     - **Never overwrite** existing data.
     - **Flag conflicts** with an inline comment in the data (if stored as text/JSON) or log them.
  5. Create **new entries** for unmatched scraped data using the most complete existing record as a template.
  6. Save the results back to the database or a JSON seed file for review.

### Database Model Update (Optional)
- Check if any new fields from the scraped data require schema changes in [backend/src/models/InsuranceProduct.js](file:///c:/Users/Balraj/Downloads/InsuranceGuide-main/backend/src/models/InsuranceProduct.js).
- Current fields in [InsuranceProduct.js](file:///c:/Users/Balraj/Downloads/InsuranceGuide-main/backend/src/models/InsuranceProduct.js) already cover many detailed attributes (eligibility, claim process, exclusions, star rating, etc.).

## Verification Plan

### Automated Tests
- Run the merge script in a dry-run mode and inspect the output JSON for:
  - Correct matching.
  - Conflict flagging.
  - Template adherence for new entries.
- Verify that existing records were not overwritten.

### Manual Verification
- Review a sample of matched and merged records in the database.
- Check the UI (React/Next.js) to ensure the newly added data is displayed correctly without layout changes.
