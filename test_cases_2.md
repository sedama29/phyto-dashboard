# Test Cases for Project Creation/Modification Workflow

## Overview
This document contains test cases for the project creation and modification functionality, covering all major features including class management, image handling, and model selection.

---

## Test Suite 1: New Project Creation

### TC-001: Create New Project - Basic Flow
**Objective:** Verify basic new project creation with minimal data

**Steps:**
1. Navigate to Project Creation page
2. Fill in all required project fields:
   - Project Code (unique)
   - Project Title
   - Description
   - Latitude/Longitude (via map or manual entry)
   - Contact information
   - Project dates
3. Click "Next" to proceed to Instrument Information
4. Add at least one instrument with required fields
5. Click "Next" to proceed to Class Definition
6. Import classes from an existing project OR create new classes
7. Add training images to classes (at least 10 images per class for training-ready status)
8. Click "Next" to proceed to AI Classifier Status
9. Verify project status and complete project creation

**Expected Results:**
- Project is created successfully
- All data is saved correctly
- Project status reflects the state (saved/training-ready)
- User can navigate through all steps without errors

---

### TC-002: Create New Project - With Model Import
**Objective:** Verify creating a new project using an existing model

**Prerequisites:** At least one existing project with trained model

**Steps:**
1. Navigate to Project Creation page
2. Fill in all required project fields
3. Proceed to Instrument Information and add instruments
4. On Class Definition page, select "Import from existing project"
5. Choose a source project that has a trained model
6. Select "Import with model" option
7. Verify classes are imported (without training images)
8. Add training images to imported classes
9. Complete project creation

**Expected Results:**
- Classes are imported from source project
- Model reference is set correctly
- Training images can be added to imported classes
- Project status reflects model reuse

---

### TC-003: Create New Project - Without Model Import
**Objective:** Verify creating a new project without model import

**Steps:**
1. Navigate to Project Creation page
2. Fill in all required project fields
3. Proceed to Instrument Information
4. On Class Definition page, select "Import from existing project"
5. Choose a source project
6. Select "Import without model" option
7. Verify classes are imported with their training images
8. Complete project creation

**Expected Results:**
- Classes are imported with training images
- No model reference is set
- Project can be completed successfully

---

## Test Suite 2: Modify Existing Project

### TC-004: Edit Existing Project - Basic Information
**Objective:** Verify editing project basic information

**Prerequisites:** An existing project

**Steps:**
1. Navigate to Project Creation page
2. Select existing project from dropdown (if available) OR navigate via edit link
3. Modify project fields (title, description, dates, etc.)
4. Save changes
5. Verify changes are persisted

**Expected Results:**
- Project information is updated correctly
- Changes are saved to database
- Updated information is displayed correctly

---

### TC-005: Edit Existing Project - Add New Classes
**Objective:** Verify adding new classes to existing project

**Prerequisites:** An existing project with some classes

**Steps:**
1. Navigate to Class Definition page for existing project
2. Click "Import from existing project" or "Add new class"
3. Add new class(es) with:
   - Class name
   - Formatted class name
   - Description
   - Training images (at least 10)
4. Save changes
5. Verify new classes appear in class list

**Expected Results:**
- New classes are added successfully
- Training images are associated correctly
- Class count increases
- New classes are marked as "new" in change log

---

### TC-006: Edit Existing Project - Modify Class Details
**Objective:** Verify modifying existing class information

**Prerequisites:** An existing project with classes

**Steps:**
1. Navigate to Class Definition page for existing project
2. Find an existing class
3. Modify:
   - Formatted class name
   - Description
4. Save changes
5. Verify changes are reflected

**Expected Results:**
- Class details are updated correctly
- Changes are tracked in change log
- Modified class is marked appropriately

---

## Test Suite 3: Class Management - Merge

### TC-007: Merge Two Classes - Basic
**Objective:** Verify merging two classes into one

**Prerequisites:** An existing project with at least 2 classes, each with training images

**Steps:**
1. Navigate to Class Definition page for existing project
2. Identify two classes to merge (Class A and Class B)
3. Click "Merge" button on Class A
4. Select Class B from merge dialog
5. Confirm merge action
6. Verify merge preview shows combined images
7. Save changes
8. Complete project modification

**Expected Results:**
- Class A and Class B are merged into target class
- All training images from both classes are combined
- Source class is deleted
- Merge action is logged in change log
- No duplicate images in merged class

---

### TC-008: Merge Classes - With Duplicate Images
**Objective:** Verify merge handles duplicate images correctly

**Prerequisites:** Two classes with some overlapping training images

**Steps:**
1. Navigate to Class Definition page
2. Merge two classes that have duplicate images
3. Verify duplicate images are not duplicated in merged class
4. Save changes

**Expected Results:**
- Duplicate images are detected and removed
- Only unique images are in merged class
- Image count is correct (no duplicates)

---

### TC-009: Merge Classes - Large Image Sets
**Objective:** Verify merge works with large number of images

**Prerequisites:** Two classes with 100+ images each

**Steps:**
1. Navigate to Class Definition page
2. Merge two classes with large image sets
3. Verify all images are merged correctly
4. Check performance (should complete in reasonable time)
5. Save changes

**Expected Results:**
- All images are merged successfully
- No images are lost
- Performance is acceptable
- Merge completes without errors

---

## Test Suite 4: Class Management - Split

### TC-010: Split Class - Basic
**Objective:** Verify splitting one class into multiple classes

**Prerequisites:** An existing project with a class containing multiple training images

**Steps:**
1. Navigate to Class Definition page
2. Identify a class to split (Class A with 50+ images)
3. Click "Split" button on Class A
4. Enter number of new classes to create (e.g., 2)
5. Fill in details for each new class:
   - Class name
   - Formatted class name
   - Description
6. Verify all images are assigned to first new class (by default)
7. Save changes
8. Complete project modification

**Expected Results:**
- Original class is removed
- New classes are created
- All images are assigned to first new class
- Split action is logged in change log
- Class count increases correctly

---

### TC-011: Split Class - Multiple New Classes
**Objective:** Verify splitting into 3+ new classes

**Prerequisites:** A class with sufficient images

**Steps:**
1. Navigate to Class Definition page
2. Split a class into 3 new classes
3. Verify all new classes are created
4. Verify images are assigned correctly
5. Save changes

**Expected Results:**
- All 3 new classes are created
- Images are assigned to first class
- Split is logged correctly

---

### TC-012: Split Class - With Image Reassignment
**Objective:** Verify ability to reassign images after split (if feature exists)

**Prerequisites:** A class with multiple images

**Steps:**
1. Split a class into 2 new classes
2. If UI allows, reassign some images from first class to second class
3. Verify image counts update correctly
4. Save changes

**Expected Results:**
- Images can be reassigned (if feature exists)
- Image counts are accurate
- Changes are saved correctly

---

## Test Suite 5: Training Image Management

### TC-013: Add Training Images - Single Class
**Objective:** Verify adding training images to a class

**Prerequisites:** A project with at least one class

**Steps:**
1. Navigate to Class Definition page
2. Select a class
3. Click "Add Images" or use drag-and-drop
4. Select multiple PNG images (10-20 images)
5. Verify images are uploaded and displayed
6. Verify image count updates
7. Save changes

**Expected Results:**
- Images are uploaded successfully
- Images are displayed in class preview
- Image count increases correctly
- Only PNG format is accepted (if restriction exists)
- Images are saved to sessionStorage temporarily

---

### TC-014: Add Training Images - Multiple Classes
**Objective:** Verify adding images to multiple classes

**Steps:**
1. Navigate to Class Definition page
2. Add images to Class A (10 images)
3. Add images to Class B (15 images)
4. Verify both classes show correct image counts
5. Save changes

**Expected Results:**
- Images are added to respective classes
- Image counts are independent for each class
- All images are saved correctly

---

### TC-015: Add Training Images - Large Batch
**Objective:** Verify adding large number of images (100+)

**Steps:**
1. Navigate to Class Definition page
2. Select a class
3. Upload 100+ images at once
4. Verify all images are processed
5. Check for performance issues
6. Verify image count is correct
7. Save changes

**Expected Results:**
- All images are uploaded successfully
- No performance degradation
- Image count is accurate
- System handles large batches gracefully

---

### TC-016: Remove Training Images - Single Image
**Objective:** Verify removing individual training images

**Prerequisites:** A class with multiple training images

**Steps:**
1. Navigate to Class Definition page
2. Select a class with images
3. Click delete/remove button on a specific image
4. Verify image is removed from preview
5. Verify image count decreases
6. Save changes

**Expected Results:**
- Image is removed from class
- Image count decreases by 1
- Removal is tracked in change log
- Image is not deleted from server until save

---

### TC-017: Remove Training Images - Multiple Images
**Objective:** Verify removing multiple images at once

**Prerequisites:** A class with 20+ training images

**Steps:**
1. Navigate to Class Definition page
2. Select a class with multiple images
3. Remove 5-10 images
4. Verify all selected images are removed
5. Verify image count updates correctly
6. Save changes

**Expected Results:**
- All selected images are removed
- Image count is accurate
- Changes are tracked correctly

---

### TC-018: Remove Training Images - All Images from Class
**Objective:** Verify removing all images from a class

**Prerequisites:** A class with training images

**Steps:**
1. Navigate to Class Definition page
2. Select a class
3. Remove all training images
4. Verify class still exists but has 0 images
5. Verify warning/notification if class needs minimum images
6. Save changes

**Expected Results:**
- All images are removed
- Class remains in list
- Appropriate warnings are shown
- Training-ready status updates if applicable

---

## Test Suite 6: Model Selection and Reuse

### TC-019: Use Existing Model - With Model Import
**Objective:** Verify using existing model when importing classes

**Prerequisites:** Source project with trained model

**Steps:**
1. Navigate to Class Definition page
2. Select "Import from existing project"
3. Choose source project with trained model
4. Select "Import with model" option
5. Verify model information is displayed
6. Verify classes are imported (without images)
7. Add training images
8. Complete project creation

**Expected Results:**
- Model reference is set correctly
- Source model project code is stored
- Classes are imported without training images
- User can add new training images
- Project status reflects model reuse

---

### TC-020: Use Existing Model - Model Choice Popup
**Objective:** Verify model choice popup appears when appropriate

**Prerequisites:** Project with classes that can trigger model choice

**Steps:**
1. Create or edit project
2. Add/import classes
3. Add training images (10+ per class)
4. Navigate to next step or save
5. Verify model choice popup appears (if conditions are met)
6. Select appropriate option (use existing model or create new)
7. Verify selection is saved

**Expected Results:**
- Popup appears at correct time
- Options are clear and understandable
- Selection is saved correctly
- Appropriate actions are taken based on selection

---

### TC-021: Create New Model - Without Reuse
**Objective:** Verify creating new model without reusing existing

**Steps:**
1. Create new project
2. Add classes with training images
3. When model choice popup appears, select "Create new model"
4. Complete project creation
5. Verify new model will be trained

**Expected Results:**
- No model reuse is set
- Project status indicates new model training needed
- Training process can be initiated

---

## Test Suite 7: Edge Cases and Error Handling

### TC-022: Duplicate Project Code
**Objective:** Verify error handling for duplicate project codes

**Steps:**
1. Navigate to Project Creation page
2. Enter a project code that already exists
3. Try to save/create project
4. Verify appropriate error message

**Expected Results:**
- Error message is displayed
- Project is not created
- User can correct the project code

---

### TC-023: Invalid Image Format
**Objective:** Verify handling of non-PNG images (if restricted)

**Steps:**
1. Navigate to Class Definition page
2. Try to upload non-PNG image (JPG, GIF, etc.)
3. Verify error handling

**Expected Results:**
- Error message for unsupported format
- Invalid images are rejected
- Only valid images are accepted

---

### TC-024: Insufficient Training Images
**Objective:** Verify handling when class has less than 10 images

**Steps:**
1. Create or edit project
2. Add class with less than 10 training images
3. Try to complete project creation
4. Verify status and warnings

**Expected Results:**
- Warning/notification about insufficient images
- Project status reflects insufficient images
- Training-ready status is 0 for class
- Project can still be saved

---

### TC-025: Maximum Image Limit
**Objective:** Verify handling of image limit (10,000 for new projects)

**Steps:**
1. Create new project
2. Import classes or add new class
3. Try to add more than 10,000 images to a class
4. Verify limit enforcement

**Expected Results:**
- Image limit is enforced
- Warning message is shown
- Excess images are rejected or limited
- Limit is clearly communicated

---

### TC-026: Session Storage Limits
**Objective:** Verify handling when sessionStorage quota is exceeded

**Steps:**
1. Create project with many classes
2. Add large number of images (base64)
3. Verify system handles quota exceeded gracefully

**Expected Results:**
- System handles quota exceeded error
- Minimal data is stored if needed
- User is notified appropriately
- System continues to function

---

### TC-027: Network Errors During Save
**Objective:** Verify handling of network errors

**Steps:**
1. Create or edit project
2. Make changes (add classes, images, etc.)
3. Simulate network error (disconnect network)
4. Try to save/submit
5. Verify error handling

**Expected Results:**
- Error message is displayed
- User data is preserved in sessionStorage
- User can retry after network is restored
- No data loss occurs

---

### TC-028: Cancel/Back Navigation
**Objective:** Verify data persistence when navigating back

**Steps:**
1. Start creating new project
2. Fill in project information
3. Navigate to Instrument Information
4. Click "Previous" to go back
5. Verify project data is still present
6. Continue and complete project

**Expected Results:**
- Data is preserved when navigating back
- All entered information is retained
- User can continue from where they left off

---

## Test Suite 8: Data Validation

### TC-029: Required Field Validation
**Objective:** Verify all required fields are validated

**Steps:**
1. Navigate to Project Creation page
2. Try to proceed without filling required fields
3. Verify validation messages
4. Fill required fields and verify validation passes

**Expected Results:**
- Required fields are clearly marked
- Validation messages are clear
- User cannot proceed without required data
- Validation works for all steps

---

### TC-030: Data Format Validation
**Objective:** Verify format validation for fields

**Steps:**
1. Test email format validation
2. Test date format validation
3. Test coordinate format validation
4. Test phone number format (if applicable)

**Expected Results:**
- Invalid formats are rejected
- Error messages are clear
- Valid formats are accepted

---

## Test Suite 9: Change Tracking

### TC-031: Change Log - All Operations
**Objective:** Verify change log tracks all modifications

**Steps:**
1. Edit existing project
2. Perform multiple operations:
   - Add new class
   - Merge two classes
   - Split a class
   - Modify class details
   - Add images
   - Remove images
3. Verify change log contains all operations
4. Complete project modification
5. Verify changes are persisted

**Expected Results:**
- All operations are logged
- Change log is accurate
- Changes are saved to database
- Change log can be reviewed

---

### TC-032: Change Log - Sequence and Timestamps
**Objective:** Verify change log has correct sequence and timestamps

**Steps:**
1. Perform multiple operations in sequence
2. Verify each operation has correct sequence number
3. Verify timestamps are accurate
4. Verify operations are in correct order

**Expected Results:**
- Sequence numbers are incremental
- Timestamps are accurate
- Operations are in chronological order

---

## Test Suite 10: Integration and Workflow

### TC-033: Complete Workflow - New Project
**Objective:** Verify complete workflow from start to finish

**Steps:**
1. Create new project (all steps)
2. Add instruments
3. Import/create classes
4. Add training images
5. Complete project creation
6. Verify project appears in project list
7. Verify project can be used for classification

**Expected Results:**
- All steps complete successfully
- Project is created and saved
- Project is available for use
- No errors occur

---

### TC-034: Complete Workflow - Edit Existing Project
**Objective:** Verify complete workflow for editing

**Steps:**
1. Open existing project for editing
2. Modify project information
3. Add new classes
4. Merge/split classes
5. Add/remove images
6. Save changes
7. Verify changes are reflected

**Expected Results:**
- All modifications are saved
- Changes are reflected correctly
- Project status updates appropriately
- No data loss occurs

---

## Test Suite 11: UI/UX Validation

### TC-035: Responsive Design
**Objective:** Verify UI works on different screen sizes

**Steps:**
1. Test on desktop (1920x1080)
2. Test on tablet (768x1024)
3. Test on smaller screens
4. Verify all elements are accessible
5. Verify forms are usable

**Expected Results:**
- UI is responsive
- All elements are accessible
- Forms are usable on all screen sizes
- No horizontal scrolling issues

---

### TC-036: Loading Indicators
**Objective:** Verify loading indicators appear during operations

**Steps:**
1. Perform operations that take time:
   - Importing classes
   - Uploading images
   - Saving project
2. Verify loading indicators appear
3. Verify indicators disappear when complete

**Expected Results:**
- Loading indicators appear appropriately
- Users are informed of progress
- Indicators are clear and visible

---

### TC-037: Error Messages
**Objective:** Verify error messages are clear and helpful

**Steps:**
1. Trigger various error conditions
2. Verify error messages are displayed
3. Verify messages are clear and actionable

**Expected Results:**
- Error messages are clear
- Messages explain what went wrong
- Messages suggest how to fix issues
- Messages are user-friendly

---

## Test Suite 12: Performance

### TC-038: Large Number of Classes
**Objective:** Verify performance with many classes

**Steps:**
1. Create project with 50+ classes
2. Add images to multiple classes
3. Verify page loads in reasonable time
4. Verify operations complete without lag

**Expected Results:**
- Page loads in < 5 seconds
- Operations complete without significant lag
- UI remains responsive
- No memory issues

---

### TC-039: Large Image Sets
**Objective:** Verify performance with large image sets

**Steps:**
1. Add class with 1000+ images
2. Verify page performance
3. Verify image preview/display works
4. Verify save operation completes

**Expected Results:**
- System handles large image sets
- Performance is acceptable
- No crashes or freezes
- Operations complete successfully

---

## Summary

### Priority Levels:
- **P0 (Critical):** TC-001, TC-004, TC-007, TC-010, TC-013, TC-016, TC-019, TC-033
- **P1 (High):** TC-002, TC-005, TC-008, TC-011, TC-014, TC-017, TC-020, TC-034
- **P2 (Medium):** TC-003, TC-006, TC-009, TC-012, TC-015, TC-018, TC-021, TC-022, TC-031
- **P3 (Low):** TC-023, TC-024, TC-025, TC-026, TC-027, TC-028, TC-029, TC-030, TC-032, TC-035, TC-036, TC-037, TC-038, TC-039

### Test Execution Notes:
1. Execute P0 tests first
2. Ensure test data is prepared before execution
3. Clean up test data after execution
4. Document any bugs found with steps to reproduce
5. Verify fixes with regression tests

