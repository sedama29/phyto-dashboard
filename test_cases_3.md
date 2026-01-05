# Manual QA Steps

## Pre-conditions
1. Ensure the Flask app is running (`python main_2.py` or whichever entry point is used).
2. Open the browser and log in using the Google Auth button so authenticated flows (save/discard/import) behave normally.
3. Clear relevant `sessionStorage` keys (`projectInfo`, `instrumentsInfo`, `aiClassifierStatus`) or use incognito / hard refresh to start from a clean slate.

## Instrument Information form
1. Navigate to the `Instrument Information` page and add an instrument to a new project.
2. Click **Edit** on the newly added row; verify the form opens with `Save` button and new `Discard` button.
3. Without saving, click **Discard** and confirm that:
   - The form hides,
   - All inputs clear,
   - The `Next`/`Previous` navigation buttons re-enable.
4. Re-open the form, make changes, and click **Save**; confirm the table updates and `sessionStorage.instrumentsInfo` reflects the change.
5. Try clicking **Save** (page-level) while the form is open—confirm you get the alert preventing save.

## Class importation flows
1. Go to `Class Importation`.
2. Use the “Import classes and training images” button to open the project selector, select a project, and confirm the modal closes and classes list populates.
3. Verify the change log (on that page) indicates `image_changes` or other structural deltas matching your chosen project.
4. Repeat with the “Import Classes from a Project” (classes-only) variant and ensure `status.changesSummary` notes “No training images”.
5. Import a project with an existing AI model (`with_model`) and confirm the page shows the `modelSource` when appropriate.
6. Make a minor edit (formatted/class description change) to one imported class and ensure the change log marks it as `modified` only (no structural change).

## AI Classifier status verification
1. After each import/edit above, go to the AI Classifier Status page.
2. Verify the messaging matches the state:
   - Image-only changes show the `Pending` badge and mention training images in the next cycle.
   - Classes-only import without images mentions “Training images will be provided” and shows “Ready to Use”.
   - Structural changes display “AI-Classifier will be generated within 24 hours” (or the insufficient data branch when <10 images).
   - Modified-only edits display “AI-Classifier model is not changed” with details about formatted names/descriptions.
3. Confirm the “Source Model” row appears only when `importType` is `with_model` or a reused model path is provided.
4. Check that the “Complete Project Creation” button is shown whenever `status.isPreview` is true.

## General checks
1. From the instrument info page, ensure `handleSave()` posts data, and success/failure alerts match the AJAX response (manual backend testing or stubbed responses).
2. Validate navigation buttons disable while forms pop up and re-enable after closure.
3. Throughout the flows, verify console/log messages for unexpected errors (browser dev tools console).

## Notes
- Document observed messages and whether they match expectations.
- If anything fails, capture screenshots and log steps to reproduce before resetting `sessionStorage`.

