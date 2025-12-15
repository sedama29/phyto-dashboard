# Comprehensive Test Cases for Class Importation and Project Management

## Overview
This document outlines comprehensive test cases to ensure class importation, project modifications, and all related operations work correctly without corrupting classified images or breaking the system.

---

## Critical Understanding: Class Import Behavior

### When Importing Classes from Another Project:

**For Existing Projects:**
- **"Import classes only" (classes_only) is ENABLED** - You can add class definitions without copying training images
- If a class name **conflicts** (same name exists), the system shows a conflict resolution dialog with options:
  - **Skip**: Don't import this class (existing class remains unchanged)
  - **Merge**: Add training images from imported class to existing class (existing class definition is preserved, images are merged)
- If a class name **doesn't conflict**, it's added as a new class
- **Classes are NOT automatically overridden** - conflicts must be explicitly resolved
- You can switch between import types (classes_only, with_images, with_model) - buttons remain enabled

**For New Projects:**
- All classes are imported without conflicts (no existing classes to conflict with)

---

## Test Case Categories

### 1. EXISTING PROJECT - Class Importation Tests

#### TC-EX-001: Import Classes Only (classes_only) - No Conflicts
**Setup:**
- Existing project "PROJ_A" with classes: ["Copepod", "Diatom"]
- Source project "PROJ_B" with classes: ["Dinoflagellate", "Cyanobacteria"]

**Steps:**
1. Open PROJ_A for editing
2. Click "Import classes from other project"
3. Select PROJ_B
4. Verify all classes from PROJ_B appear in the list
5. Submit

**Expected Results:**
- PROJ_A now has: ["Copepod", "Diatom", "Dinoflagellate", "Cyanobacteria"]
- **NO training images copied** (this is classes_only import)
- Class definitions (name, formatted_name, description) imported
- **NO model training triggered** (no training images imported)
- Project status unchanged (unless user manually adds >=10 training images later)
- **Classified images in PROJ_A remain unchanged**
- No conflicts shown

**Verification:**
- Check `class_details` table for PROJ_A - new classes present
- Check `image_data` table - all existing classifications remain intact
- Verify NO training images in `/training_data/PROJ_A/` for new classes (Dinoflagellate, Cyanobacteria)
- Verify NO model training triggered

---

#### TC-EX-002: Import Classes Only (classes_only) - With Conflicts (Skip)
**Setup:**
- Existing project "PROJ_A" with class "Copepod" (50 training images, 1000 classified images)
- Source project "PROJ_B" with class "Copepod" (30 training images, different description)

**Steps:**
1. Open PROJ_A for editing
2. Click "Import classes from other project"
3. Select PROJ_B
4. Conflict dialog appears for "Copepod"
5. Choose "Skip" for Copepod
6. Submit

**Expected Results:**
- PROJ_A's "Copepod" class remains unchanged
- No training images added
- Description unchanged
- **All 1000 classified images remain linked to "Copepod"**
- No model retraining (no changes made)

**Verification:**
- Check `class_details` - PROJ_A's Copepod unchanged
- Check `image_data` - all 1000 images still classified as "Copepod"
- Check training images count unchanged

---

#### TC-EX-003: Import Classes Only (classes_only) - With Conflicts (Merge)
**Setup:**
- Existing project "PROJ_A" with class "Copepod" (50 training images, 1000 classified images)
- Source project "PROJ_B" with class "Copepod" (30 training images, different description)

**Steps:**
1. Open PROJ_A for editing
2. Click "Import classes from other project"
3. Select PROJ_B
4. Conflict dialog appears for "Copepod"
5. Choose "Merge" for Copepod
6. Submit

**Expected Results:**
- PROJ_A's "Copepod" class keeps its original definition (name, formatted_name, description)
- **NO training images are copied from PROJ_B** (this is classes_only import)
- PROJ_A's Copepod still has 50 training images (unchanged)
- **All 1000 classified images remain linked to "Copepod"**
- **No model training triggered** (no changes to training images)
- Project status remains unchanged (unless classes have >=10 images from manual additions)

**Verification:**
- Check `class_details` - PROJ_A's Copepod definition unchanged
- Check training images count - still 50 (NOT increased to 80)
- Check `image_data` - all 1000 images still classified as "Copepod"
- Verify NO model training triggered (no image changes)
- Check `/training_data/PROJ_A/Copepod/` - no new images from PROJ_B

---

#### TC-EX-004: Import Classes and Training Images (with_images) - No Modifications
**Setup:**
- Existing project "PROJ_A" with classes: ["Copepod", "Diatom"] (already trained, status=1)
- Source project "PROJ_B" with same classes: ["Copepod", "Diatom"] (same names, same training images)

**Steps:**
1. Open PROJ_A for editing
2. Click "Import classes and training images"
3. Select PROJ_B
4. Verify classes appear (no conflicts if names match exactly)
5. Don't make any modifications (no splits, merges, deletes, image changes)
6. Submit

**Expected Results:**
- Training images copied from PROJ_B to PROJ_A
- **Model from PROJ_B is REUSED** (no new training)
- Project status remains 1 (completed)
- `source_model_project_code` set to PROJ_B
- **Classified images remain unchanged**

**Verification:**
- Check `projects` table - `source_model_project_code = PROJ_B`, `status = 1`
- Check training images copied to `/training_data/PROJ_A/`
- Check `image_data` - all existing classifications intact
- Verify no new model training triggered

---

#### TC-EX-005: Import Classes and Training Images (with_images) - With Modifications
**Setup:**
- Existing project "PROJ_A" with classes: ["Copepod", "Diatom"]
- Source project "PROJ_B" with classes: ["Copepod", "Diatom", "Dinoflagellate"]

**Steps:**
1. Open PROJ_A for editing
2. Click "Import classes and training images"
3. Select PROJ_B
4. Import all classes
5. Delete one training image from "Copepod"
6. Submit

**Expected Results:**
- Training images copied from PROJ_B
- **New model WILL BE TRAINED** (modifications detected)
- Project status set to 0 (needs training)
- `source_model_project_code` set to NULL
- **Classified images remain unchanged until new model classifies them**

**Verification:**
- Check `projects` table - `status = 0`, `source_model_project_code = NULL`
- Check training images copied
- Verify model training triggered
- Check `image_data` - existing classifications still present

---

#### TC-EX-006: Import Classes and Model (with_model)
**Setup:**
- Existing project "PROJ_A" with classes: ["Copepod", "Diatom"] (some classified images)
- Source project "PROJ_B" with trained model and classes: ["Copepod", "Diatom", "Dinoflagellate"]

**Steps:**
1. Open PROJ_A for editing
2. Click "Import classes and model"
3. Select PROJ_B
4. Submit

**Expected Results:**
- **NO classes copied**
- **NO training images copied**
- `source_model_project_code` set to PROJ_B
- Project status set to 1 (completed)
- PROJ_A will use PROJ_B's model for classification
- **Existing classified images in PROJ_A remain in database**
- PROJ_A can now classify using PROJ_B's model (which has different classes)

**Verification:**
- Check `projects` table - `source_model_project_code = PROJ_B`, `status = 1`
- Check `class_details` - PROJ_A's classes unchanged
- Check training images - none copied
- Check `image_data` - existing classifications remain
- **CRITICAL**: Verify that PROJ_A can now classify images using PROJ_B's model (which may have different class names)

---

#### TC-EX-007: Import Classes - Partial Selection (Uncheck Some Classes)
**Setup:**
- Existing project "PROJ_A" with class "Copepod"
- Source project "PROJ_B" with classes: ["Copepod", "Diatom", "Dinoflagellate"]

**Steps:**
1. Open PROJ_A for editing
2. Click "Import classes and training images" (or "Import classes from other project" for classes_only)
3. Select PROJ_B
4. Uncheck "Copepod" (skip conflict if using with_images, or just don't select it)
5. Check "Diatom" and "Dinoflagellate"
6. Submit

**Expected Results:**
- Only "Diatom" and "Dinoflagellate" imported
- "Copepod" not imported (skipped/unchecked)
- **If using with_images:** Training images copied only for selected classes (Diatom, Dinoflagellate)
- **If using classes_only:** NO training images copied
- **Existing "Copepod" class in PROJ_A unchanged**
- **All classified images for "Copepod" remain intact**

**Verification:**
- Check `class_details` - PROJ_A has: ["Copepod" (original), "Diatom", "Dinoflagellate"]
- **If using with_images:** Check training images - only for Diatom and Dinoflagellate
- **If using classes_only:** Check training images - NONE copied
- Check `image_data` - Copepod classifications unchanged

---

### 2. EXISTING PROJECT - Training Image Management Tests

#### TC-EX-008: Add Training Images to Existing Class
**Setup:**
- Existing project "PROJ_A" with class "Copepod" (50 training images, 1000 classified images)

**Steps:**
1. Open PROJ_A for editing
2. Select "Copepod" class
3. Click "Add Images"
4. Upload 20 new training images
5. Submit

**Expected Results:**
- Copepod now has 70 training images
- New model will be trained (image changes detected)
- Project status set to 0 (needs training)
- **All 1000 classified images remain linked to "Copepod"**
- Class definition unchanged

**Verification:**
- Check training images count increased
- Check `image_data` - all classifications intact
- Verify model training triggered

---

#### TC-EX-009: Delete Training Images from Existing Class
**Setup:**
- Existing project "PROJ_A" with class "Copepod" (50 training images, 1000 classified images)

**Steps:**
1. Open PROJ_A for editing
2. Select "Copepod" class
3. Delete 10 training images
4. Submit

**Expected Results:**
- Copepod now has 40 training images
- New model will be trained (image changes detected)
- Project status set to 0 (needs training)
- **All 1000 classified images remain linked to "Copepod"**
- **Deleted training images removed from `/training_data/` folder**

**Verification:**
- Check training images count decreased
- Check `image_data` - all classifications intact
- Verify deleted images removed from filesystem
- Verify model training triggered

---

#### TC-EX-010: Delete Training Images - Class Falls Below 10 Images
**Setup:**
- Existing project "PROJ_A" with class "Copepod" (15 training images, 1000 classified images)

**Steps:**
1. Open PROJ_A for editing
2. Select "Copepod" class
3. Delete 10 training images (now has 5)
4. Submit

**Expected Results:**
- Copepod has 5 training images (< 10 threshold)
- Class marked as `training_ready = 0` (not ready for training)
- **Class still exists in database**
- **All 1000 classified images remain linked to "Copepod"**
- Model training may skip this class or fail if no other classes have enough images

**Verification:**
- Check `class_details` - `training_ready = 0`
- Check `image_data` - all classifications intact
- Verify class still exists (not deleted)

---

### 3. EXISTING PROJECT - Class Modification Tests

#### TC-EX-011: Merge Two Classes
**Setup:**
- Existing project "PROJ_A" with classes: ["Copepod_Large", "Copepod_Small"]
- "Copepod_Large": 30 training images, 500 classified images
- "Copepod_Small": 25 training images, 300 classified images

**Steps:**
1. Open PROJ_A for editing
2. Merge "Copepod_Small" into "Copepod_Large"
3. Name merged class "Copepod"
4. Submit

**Expected Results:**
- New class "Copepod" created with 55 training images (merged)
- "Copepod_Large" and "Copepod_Small" deleted
- **All 500 classified images from "Copepod_Large" updated to "Copepod"**
- **All 300 classified images from "Copepod_Small" updated to "Copepod"**
- Images moved in filesystem from old folders to new folder
- New model will be trained

**Verification:**
- Check `class_details` - only "Copepod" exists
- Check `image_data` - all 800 images now classified as "Copepod"
- Check filesystem - images moved to `/data/PROJ_A/YYYY/MM/Copepod/`
- Verify model training triggered

---

#### TC-EX-012: Split One Class into Two
**Setup:**
- Existing project "PROJ_A" with class "Copepod" (50 training images, 1000 classified images)

**Steps:**
1. Open PROJ_A for editing
2. Split "Copepod" into "Copepod_Large" and "Copepod_Small"
3. Assign 30 training images to "Copepod_Large", 20 to "Copepod_Small"
4. Submit

**Expected Results:**
- "Copepod" deleted
- "Copepod_Large" and "Copepod_Small" created
- Training images distributed
- **CRITICAL: What happens to the 1000 classified images?**
  - Option A: All remain as "Copepod" (orphaned class name)
  - Option B: User must manually reclassify
  - Option C: System prompts for reclassification
- New model will be trained

**Verification:**
- Check `class_details` - "Copepod" deleted, new classes created
- Check `image_data` - verify what happened to classified images
- Verify model training triggered

---

#### TC-EX-013: Delete a Class
**Setup:**
- Existing project "PROJ_A" with class "Copepod" (50 training images, 1000 classified images)

**Steps:**
1. Open PROJ_A for editing
2. Delete "Copepod" class
3. Submit

**Expected Results:**
- "Copepod" class deleted from `class_details`
- Training images removed from `/training_data/`
- **CRITICAL: What happens to 1000 classified images?**
  - Option A: All deleted from `image_data`
  - Option B: All remain but with invalid class_name
  - Option C: Moved to "Unclassified" or deleted
- New model will be trained

**Verification:**
- Check `class_details` - "Copepod" deleted
- Check `image_data` - verify classified images handling
- Check filesystem - training images removed
- Verify model training triggered

---

#### TC-EX-014: Modify Class Definition (Name, Description)
**Setup:**
- Existing project "PROJ_A" with class "Copepod" (50 training images, 1000 classified images)

**Steps:**
1. Open PROJ_A for editing
2. Change "Copepod" formatted_name to "Copepod (Large)"
3. Change description
4. Submit

**Expected Results:**
- Class definition updated in `class_details`
- **All 1000 classified images remain linked (class_name unchanged)**
- Training images unchanged
- New model will be trained (class modified)

**Verification:**
- Check `class_details` - definition updated
- Check `image_data` - all still classified as "Copepod" (class_name unchanged)
- Verify model training triggered

---

### 4. NEW PROJECT - Class Importation Tests

#### TC-NEW-001: Create New Project - Import Classes Only
**Setup:**
- New project "PROJ_NEW" (no existing classes)
- Source project "PROJ_B" with classes: ["Copepod", "Diatom", "Dinoflagellate"] (each with >10 training images)

**Steps:**
1. Create new project PROJ_NEW
2. Navigate to class definition page
3. Click "Import classes from other project"
4. Select PROJ_B
5. Submit

**Expected Results:**
- All classes imported: ["Copepod", "Diatom", "Dinoflagellate"]
- **NO training images copied** (this is classes_only import)
- Class definitions (name, formatted_name, description) imported
- **NO model training triggered** (no training images imported)
- Project status set to -1 (hidden) or current status (no training images means no model can be trained yet)
- User must manually add training images (>=10 per class) before model can be trained

**Verification:**
- Check `class_details` - all classes present with definitions
- Check training images - **NONE copied** (verify `/training_data/PROJ_NEW/` is empty or only has manually added images)
- Check project status - should be -1 or unchanged
- Verify NO model training triggered

---

#### TC-NEW-002: Create New Project - Import Classes and Training Images
**Setup:**
- New project "PROJ_NEW" (no existing classes)
- Source project "PROJ_B" with trained model and classes

**Steps:**
1. Create new project PROJ_NEW
2. Navigate to class definition page
3. Click "Import classes and training images"
4. Select PROJ_B
5. Don't make modifications
6. Submit

**Expected Results:**
- All classes imported
- Training images copied
- **Model from PROJ_B is REUSED** (no new training)
- Project status set to 1 (completed)
- `source_model_project_code` set to PROJ_B

**Verification:**
- Check `class_details` - all classes present
- Check training images copied
- Check `projects` - `status = 1`, `source_model_project_code = PROJ_B`
- Verify no model training triggered

---

#### TC-NEW-003: Create New Project - Import Classes and Model
**Setup:**
- New project "PROJ_NEW" (no existing classes)
- Source project "PROJ_B" with trained model

**Steps:**
1. Create new project PROJ_NEW
2. Navigate to class definition page
3. Click "Import classes and model"
4. Select PROJ_B
5. Submit

**Expected Results:**
- **NO classes copied**
- **NO training images copied**
- `source_model_project_code` set to PROJ_B
- Project status set to 1 (completed)
- PROJ_NEW will use PROJ_B's model for classification

**Verification:**
- Check `class_details` - empty (no classes)
- Check training images - none
- Check `projects` - `status = 1`, `source_model_project_code = PROJ_B`

---

#### TC-NEW-004: Create New Project - Add New Class Manually
**Setup:**
- New project "PROJ_NEW" (no existing classes)

**Steps:**
1. Create new project PROJ_NEW
2. Navigate to class definition page
3. Click "+ Add new class"
4. Create class "Copepod" with 15 training images
5. Submit

**Expected Results:**
- "Copepod" class created
- Training images saved
- New model will be trained (15 images >= 10 threshold)
- Project status set to 0 (needs training) or 1 (if training completes)

**Verification:**
- Check `class_details` - "Copepod" present
- Check training images saved
- Verify model training triggered

---

### 5. PROJECT INFORMATION CHANGES

#### TC-INFO-001: Update Project Form (Title, Description, POC, etc.)
**Setup:**
- Existing project "PROJ_A" with classes and classified images

**Steps:**
1. Open PROJ_A for editing
2. Change project title, description, POC info
3. Don't modify classes
4. Submit

**Expected Results:**
- Project information updated in `projects` table
- **All classes unchanged**
- **All classified images unchanged**
- **All training images unchanged**
- Project status unchanged
- Model unchanged

**Verification:**
- Check `projects` table - info updated
- Check `class_details` - unchanged
- Check `image_data` - unchanged
- Check training images - unchanged

---

#### TC-INFO-002: Update Instrument Information
**Setup:**
- Existing project "PROJ_A" with instruments and classified images

**Steps:**
1. Open PROJ_A for editing
2. Navigate to instrument info page
3. Update instrument IP, username, full_path
4. Submit

**Expected Results:**
- Instrument information updated in `ifcb_data` table
- **All classes unchanged**
- **All classified images unchanged**
- **All training images unchanged**
- Project status unchanged

**Verification:**
- Check `ifcb_data` - info updated
- Check `class_details` - unchanged
- Check `image_data` - unchanged

---

### 6. EDGE CASES AND ERROR SCENARIOS

#### TC-EDGE-001: Import from Project with No Classes
**Setup:**
- Existing project "PROJ_A"
- Source project "PROJ_EMPTY" with no classes

**Steps:**
1. Open PROJ_A for editing
2. Click "Import classes from other project"
3. Select PROJ_EMPTY
4. Submit

**Expected Results:**
- Error message: "No classes found in selected project"
- No changes made to PROJ_A
- **All existing data unchanged**

**Verification:**
- Check error handling
- Verify no changes to PROJ_A

---

#### TC-EDGE-002: Import from Project with Incomplete Training (<10 images per class)
**Setup:**
- Existing project "PROJ_A"
- Source project "PROJ_B" with classes having <10 training images each

**Steps:**
1. Open PROJ_A for editing
2. Click "Import classes and training images"
3. Select PROJ_B
4. Submit

**Expected Results:**
- Classes imported
- Training images copied
- Classes marked as `training_ready = 0`
- Model training may fail or skip these classes
- **Classified images unchanged**

**Verification:**
- Check `class_details` - `training_ready = 0`
- Verify model training behavior

---

#### TC-EDGE-003: Import Classes - Network/Server Error During Import
**Setup:**
- Existing project "PROJ_A" with classified images
- Network interruption during import

**Steps:**
1. Open PROJ_A for editing
2. Start importing classes
3. Simulate network error mid-import
4. Check system state

**Expected Results:**
- Import fails gracefully
- **No partial data saved**
- **All existing data unchanged**
- Error message displayed
- User can retry

**Verification:**
- Check database - no partial imports
- Check `image_data` - unchanged
- Verify error handling

---

#### TC-EDGE-004: Concurrent Modifications (Two Users Editing Same Project)
**Setup:**
- Project "PROJ_A" accessible by two users

**Steps:**
1. User A opens PROJ_A for editing
2. User B opens PROJ_A for editing
3. User A imports classes and submits
4. User B modifies classes and submits

**Expected Results:**
- Last submit wins (or conflict detection)
- **No data corruption**
- **Classified images remain consistent**

**Verification:**
- Check for conflict handling
- Verify data integrity
- Check `image_data` consistency

---

#### TC-EDGE-005: Import Classes - Source Project Deleted After Import
**Setup:**
- Existing project "PROJ_A"
- Source project "PROJ_B" used for import

**Steps:**
1. Import classes from PROJ_B to PROJ_A
2. Delete PROJ_B
3. Verify PROJ_A still works

**Expected Results:**
- PROJ_A continues to function
- If model was reused, verify it still works
- **Classified images unchanged**

**Verification:**
- Check PROJ_A functionality
- Verify model availability
- Check `image_data` intact

---

### 7. CLASSIFIED IMAGES INTEGRITY TESTS

#### TC-INTEGRITY-001: Verify Classified Images After Class Merge
**Setup:**
- Project "PROJ_A" with 1000 images classified as "Copepod_Large"
- Project "PROJ_A" with 500 images classified as "Copepod_Small"

**Steps:**
1. Merge "Copepod_Small" into "Copepod_Large" → "Copepod"
2. Verify all 1500 images

**Expected Results:**
- All 1500 images in `image_data` have `class_name = "Copepod"`
- Images moved in filesystem to "Copepod" folder
- **No images lost or orphaned**

**Verification:**
- Query `image_data`: `SELECT COUNT(*) WHERE class_name = 'Copepod'` = 1500
- Verify filesystem: all images in correct folder
- Check for orphaned images

---

#### TC-INTEGRITY-002: Verify Classified Images After Class Split
**Setup:**
- Project "PROJ_A" with 1000 images classified as "Copepod"

**Steps:**
1. Split "Copepod" into "Copepod_Large" and "Copepod_Small"
2. Verify classified images

**Expected Results:**
- **CRITICAL: Determine expected behavior:**
  - Option A: All 1000 images remain as "Copepod" (orphaned, needs reclassification)
  - Option B: User prompted to redistribute images
  - Option C: Images automatically redistributed (how?)
- **No images lost from database**

**Verification:**
- Check `image_data` - verify all 1000 images accounted for
- Check for orphaned class names
- Verify reclassification process

---

#### TC-INTEGRITY-003: Verify Classified Images After Class Deletion
**Setup:**
- Project "PROJ_A" with 1000 images classified as "Copepod"

**Steps:**
1. Delete "Copepod" class
2. Verify classified images

**Expected Results:**
- **CRITICAL: Determine expected behavior:**
  - Option A: All 1000 images deleted from `image_data`
  - Option B: All 1000 images remain with invalid `class_name = "Copepod"`
  - Option C: Images moved to "Unclassified" or default class
- **No orphaned files in filesystem**

**Verification:**
- Check `image_data` - verify handling of deleted class
- Check filesystem - verify image files handling
- Check for data integrity issues

---

#### TC-INTEGRITY-004: Verify Classified Images After Import with Model (with_model)
**Setup:**
- Project "PROJ_A" with 1000 images classified as "Copepod" (using PROJ_A's model)
- Source project "PROJ_B" with different classes: ["Diatom", "Dinoflagellate"]

**Steps:**
1. Import classes and model from PROJ_B
2. PROJ_A now uses PROJ_B's model
3. Verify existing classified images

**Expected Results:**
- PROJ_A uses PROJ_B's model (which doesn't have "Copepod" class)
- **1000 images still in `image_data` with `class_name = "Copepod"`**
- **These images are now "orphaned" (class doesn't exist in model)**
- New classifications will use PROJ_B's classes

**Verification:**
- Check `image_data` - 1000 "Copepod" images still present
- Check `class_details` - PROJ_A's classes unchanged (or verify behavior)
- Verify model can classify new images with PROJ_B's classes

---

### 8. MODEL TRAINING AND REUSE TESTS

#### TC-MODEL-001: Model Reuse - No Modifications
**Setup:**
- Existing project "PROJ_A"
- Source project "PROJ_B" with trained model

**Steps:**
1. Import classes and training images from PROJ_B
2. Make NO modifications
3. Submit

**Expected Results:**
- Model from PROJ_B is reused
- `source_model_project_code = PROJ_B`
- Project status = 1 (completed)
- No model training triggered
- **Classified images unchanged**

**Verification:**
- Check `projects` table
- Verify no training process started
- Check model availability

---

#### TC-MODEL-002: Model Training Triggered - Image Changes Only
**Setup:**
- Existing project "PROJ_A" with trained model

**Steps:**
1. Add 5 training images to existing class
2. Submit

**Expected Results:**
- Model training triggered
- Project status = 0 (needs training)
- `source_model_project_code = NULL`
- **Classified images unchanged until new model classifies**

**Verification:**
- Check project status
- Verify training triggered
- Check `image_data` unchanged

---

#### TC-MODEL-003: Model Training Triggered - Class Changes
**Setup:**
- Existing project "PROJ_A" with trained model

**Steps:**
1. Merge two classes
2. Submit

**Expected Results:**
- Model training triggered
- Project status = 0 (needs training)
- `source_model_project_code = NULL`
- **Classified images updated (merged classes)**

**Verification:**
- Check project status
- Verify training triggered
- Check `image_data` updated correctly

---

## Test Execution Checklist

### Pre-Test Setup
- [ ] Backup database
- [ ] Backup training images directory
- [ ] Backup classified images directory
- [ ] Create test projects with known data
- [ ] Document initial state (class counts, image counts, etc.)

### Test Execution
- [ ] Execute each test case
- [ ] Document actual results vs expected results
- [ ] Take screenshots of critical steps
- [ ] Log any errors or unexpected behavior
- [ ] Verify database integrity after each test

### Post-Test Verification
- [ ] Verify no data corruption
- [ ] Verify classified images integrity
- [ ] Verify training images integrity
- [ ] Verify model functionality
- [ ] Check for orphaned data
- [ ] Verify filesystem consistency

### Critical Areas to Monitor
1. **Classified Images (`image_data` table)**
   - Count should remain consistent (unless explicitly modified)
   - `class_name` should always reference valid class
   - No orphaned records

2. **Training Images (filesystem)**
   - Files should match database records
   - No orphaned files
   - Proper folder structure

3. **Class Definitions (`class_details` table)**
   - Consistency with training images
   - Consistency with classified images
   - No orphaned classes

4. **Model References**
   - `source_model_project_code` correctly set
   - Model availability when referenced
   - Training triggered when needed

---

## Questions to Resolve

1. **What happens to classified images when a class is split?**
   - Current behavior needs to be documented
   - Should system prompt for reclassification?
   - Should images be automatically redistributed?

2. **What happens to classified images when a class is deleted?**
   - Are they deleted?
   - Are they orphaned?
   - Should they be moved to a default class?

3. **What happens when importing model (with_model) but existing classified images use different class names?**
   - Are they orphaned?
   - Should system warn user?
   - Should reclassification be triggered?

4. **Concurrent access handling?**
   - How are conflicts handled?
   - Is locking implemented?
   - What happens if two users modify simultaneously?

5. **Rollback mechanism?**
   - Can changes be undone?
   - Is there a backup/restore process?
   - How to recover from errors?

---

## Notes

- All test cases should be run in a **test environment** first
- **Never test on production data** without backup
- Document any deviations from expected behavior
- Update this document as system behavior is clarified
- Add more test cases as edge cases are discovered

