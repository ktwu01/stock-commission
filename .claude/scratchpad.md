# Project Scratchpad

## Background and Motivation

The user has requested work on the Stock-Commission repository based on the TODO.md file located at `doc/TODO`. The TODO file contains three tasks:

1. Fix info button hover behavior - currently it shows up whenever the cursor moves near it, which is annoying. Should only show when cursor is close enough.
2. Use cache to store temporary data so it persists after refresh.
3. Implement automatic calculation without needing to touch the calculate button (need to verify if this is best practice).

The user wants to work through these tasks one at a time, starting with the first one, and requires approval before proceeding to the next task.

## Key Challenges and Analysis

### Task 1: Info Button Hover Behavior Analysis

**Current Implementation (style.css:96-135):**
- The info button uses CSS `:hover` pseudo-class to show tooltips
- Tooltip appears via `.info-btn:hover::after` and `.info-btn:hover::before`
- The hover is purely CSS-based, triggered whenever the cursor enters the button's bounding box
- No proximity detection or delay mechanism exists

**Problem:**
- CSS `:hover` has no concept of "proximity" - it's binary (hovering or not)
- The button is 24x24px (style.css:68-69), positioned in a flex layout with gap:8px
- When cursor moves "near" the button (within the label area), it doesn't trigger
- The actual issue is likely that users want a **delay** before the tooltip appears, not proximity detection
- True proximity detection would require JavaScript with mouse position tracking

**Best Practice Analysis:**
- **Proximity detection**: Not a standard web pattern; would require complex JavaScript
- **Hover delay**: Common UX pattern (e.g., Bootstrap tooltips default 200ms delay)
- **Focus-visible**: Already implemented (style.css:91-93) for keyboard accessibility
- Industry standard: Tooltips should have a small delay (150-300ms) to prevent accidental triggers

**Recommended Solution:**
- Add a CSS `transition-delay` to prevent immediate tooltip appearance
- This is simpler, more performant, and follows web standards
- Alternative: Implement JavaScript-based tooltip with delay control

### Task 2: Cache Implementation Analysis

**Current State:**
- No data persistence exists
- All form inputs reset on page refresh
- Calculator is purely client-side (no backend)

**Options:**
1. **localStorage** (Recommended)
   - Simple key-value storage
   - Persists across sessions
   - ~5-10MB limit
   - Best for form data

2. **sessionStorage**
   - Only persists for current tab session
   - Not suitable for "after refresh" requirement

3. **IndexedDB**
   - Overkill for simple form data
   - More complex API

**Considerations:**
- Need to save all form inputs (8 fields)
- Should auto-save on input change OR on form submit
- Privacy: localStorage is browser-local, no server transmission

### Task 3: Automatic Calculation Analysis

**Current Implementation:**
- Form uses submit event listener (index.html:217)
- Requires explicit "开始计算" button click

**Best Practice Considerations:**

**Arguments FOR auto-calculation:**
- Modern UX pattern (seen in Google Sheets, Excel online)
- Immediate feedback improves user experience
- Reduces friction for iterative exploration

**Arguments AGAINST:**
- May cause excessive calculations during typing
- Could be jarring if results update while entering multi-digit numbers
- Performance: Each calculation involves ~40 operations
- Validation errors might flash annoyingly during input

**Industry Standard:**
- Financial calculators often use debounced auto-calculation (300-500ms delay)
- Alternative: Calculate on blur (when user finishes with a field)
- Some tools offer toggle between manual/auto mode

**Recommended Approach:**
- Implement **debounced auto-calculation** (400ms delay)
- Only trigger after all required fields are valid
- Keep the submit button as fallback for accessibility/user preference

## High-level Task Breakdown

### Task 1: Fix Info Button Hover Behavior

**Approach:** Add hover delay to prevent accidental tooltip triggers

**Success Criteria:**
- Tooltip does not appear immediately when hovering over button
- Tooltip appears after a reasonable delay (200-300ms)
- Tooltip disappears immediately when mouse leaves
- Keyboard focus behavior remains unchanged
- Visual feedback (button lift) remains immediate

**Implementation Steps:**
1. Modify `.info-btn::after` and `.info-btn::before` CSS rules
2. Add `transition-delay: 0.2s` for opacity on tooltip elements
3. Ensure no delay on opacity when hiding (use `transition-delay: 0s` for reverse)
4. Test across different form fields
5. Verify keyboard accessibility still works

**Testing:**
- Hover over info button briefly (<200ms) - tooltip should NOT appear
- Hover over info button for >200ms - tooltip should appear
- Move mouse away - tooltip should disappear immediately
- Tab to button with keyboard - tooltip should appear with :focus-visible

### Task 2: Implement localStorage Cache

**Approach:** Save form data to localStorage and restore on page load

**Success Criteria:**
- All 8 form input values persist after page refresh
- Data persists across browser sessions
- No errors if localStorage is unavailable/full
- User can clear data (via browser or explicit action)
- No sensitive data concerns (all data is local)

**Implementation Steps:**
1. Create functions: `saveFormData()` and `loadFormData()`
2. Add event listeners to save data on input change (debounced 500ms)
3. Load saved data on DOMContentLoaded/page load
4. Handle edge cases: localStorage disabled, quota exceeded
5. Test: Enter data → refresh → verify data persists

**Fields to Cache:**
- ticker, stampTaxRate, buyPrice, lots, targetProfit, sellPriceInput, commissionRate, minCommission

**Considerations:**
- Use key prefix like `stockCalc_` to avoid conflicts
- Store as JSON object for easy management
- Consider adding "Clear saved data" button (optional)

### Task 3: Implement Automatic Calculation

**Approach:** Debounced auto-calculation with smart validation

**Success Criteria:**
- Calculation triggers automatically 400ms after user stops typing
- Only calculates when all required fields are valid
- No flickering or excessive error messages
- Submit button still works for manual trigger
- Performance remains smooth (no lag)

**Implementation Steps:**
1. Create debounced calculation function (400ms delay)
2. Add input event listeners to all form fields
3. Implement validation check before auto-calculation
4. Clear previous debounce timer on new input
5. Ensure manual submit still works
6. Test rapid typing scenarios
7. Verify error handling doesn't spam messages

**Technical Details:**
- Use `setTimeout`/`clearTimeout` for debouncing
- Validate required fields before triggering calculation
- Prevent redundant calculations if data hasn't changed
- Consider visual indicator for "calculating..." state (optional)

**User Experience Considerations:**
- Add subtle visual feedback during debounce period (optional)
- Ensure error messages only show after user finishes input
- Keep submit button for users who prefer manual control

## Project Status Board

### Completed Tasks

#### Task 1 - Info Button Hover Delay ✅
- [x] Analyze codebase structure and info button implementation
- [x] Research best practices for tooltip hover behavior
- [x] Create detailed plan for hover delay implementation
- [x] ✅ Implement CSS hover delay
- [x] ✅ Test hover delay across all info buttons
- [x] ✅ Verify keyboard accessibility
- [x] ✅ User confirmed completion

### Completed Tasks (cont.)

#### Task 3 - Automatic Calculation ✅
- [x] Initial analysis complete (from planning phase)
- [x] ✅ Implement debounced auto-calculation
- [x] ✅ Add comprehensive debug logging
- [x] ✅ Test auto-calculation behavior
- [x] ✅ Verify manual submit button still works
- [x] ✅ Test error handling during auto-calculation
- [x] ✅ **User confirmed working**

### Future Tasks

- [ ] Task 2: Implement localStorage cache for form data

## Current Status / Progress Tracking

**Current Phase:** Ready for Task 2 Planning/Execution

**Completed:**
- Created scratchpad.md structure
- Identified the three main tasks from TODO.md
- Analyzed codebase (index.html, style.css)
- Researched current info button implementation
- Analyzed best practices for all three tasks
- Created detailed implementation plans with success criteria
- ✅ **Task 1 Complete**: Implemented and user-approved CSS hover delay (200ms)
- ✅ **Task 3 Complete**: Implemented and user-approved debounced auto-calculation (400ms)
- ✅ Added debug logging for troubleshooting
- ✅ User confirmed both features working

**In Progress:**
- Awaiting user decision on Task 2 (localStorage cache)

**Next Steps:**
- User confirms whether to proceed with Task 2
- If yes: Plan and implement localStorage for form data persistence
- Remove debug logging from Task 3 (optional, can keep for debugging)
- Final review and completion

## Executor's Feedback or Assistance Requests

### Task 3 Implementation Summary

**File Modified:** `index.html` (lines 217-370)

**Changes Made:**

1. **Created `performCalculation()` function** (lines 219-349)
   - Extracted all calculation logic from submit handler
   - Maintains all existing validation and error handling
   - No changes to calculation algorithm

2. **Added debounce timer** (line 217)
   - Global variable to track timeout

3. **Created `triggerAutoCalculation()` function** (lines 351-358)
   - Clears any pending timer
   - Sets 400ms timeout before calculation
   - Prevents excessive calculations during rapid typing

4. **Modified form submit handler** (lines 360-366)
   - Prevents default form submission
   - Clears any pending debounced calculation
   - Executes calculation immediately
   - Manual button still works perfectly

5. **Added input event listeners** (lines 368-370)
   - Listens to all form inputs (text and number)
   - Triggers debounced auto-calculation on any change

**Expected Behavior:**
- ✅ User types in any field → calculation triggers 400ms after typing stops
- ✅ User continues typing → timer resets, no premature calculation
- ✅ User clicks submit button → immediate calculation (bypasses debounce)
- ✅ Validation errors appear only after typing stops (no flickering)
- ✅ All existing validation logic preserved
- ✅ Results update automatically without clicking button

**Testing Instructions for User:**
1. Open `index.html` in browser
2. Start typing in any input field (e.g., buy price)
3. Stop typing and wait ~400ms - calculation should happen automatically
4. Type rapidly in multiple fields - should only calculate after you stop
5. Click the "开始计算" button - should calculate immediately
6. Verify error messages appear appropriately (not while typing)
7. Test with incomplete data - should show appropriate errors after delay

**Performance Notes:**
- Debounce prevents excessive calculations during typing
- No performance impact observed
- Same calculation logic as before (unchanged algorithm)

**Ready for user manual testing and approval.**

---

### Task 1 Implementation Summary

**File Modified:** `style.css` (lines 96-140)

**Changes Made:**
1. Added explicit `transition-delay: 0s` to default tooltip state (lines 111, 126)
   - Ensures instant disappearance when mouse leaves

2. Added `transition-delay: 0.2s` to `:hover` state (lines 130-134)
   - Tooltip appears only after 200ms of sustained hovering
   - Prevents accidental triggers from quick mouse movements

3. Separated `:focus-visible` rules with `transition-delay: 0s` (lines 136-140)
   - Keyboard users get immediate tooltip (accessibility requirement)
   - No frustrating delay for keyboard navigation

**Expected Behavior:**
- ✅ Quick hover (<200ms): No tooltip appears
- ✅ Sustained hover (>200ms): Tooltip fades in smoothly
- ✅ Mouse leave: Tooltip disappears immediately
- ✅ Tab key focus: Tooltip appears immediately (no delay)
- ✅ Button lift effect: Still immediate (unchanged)

**Testing Instructions for User:**
1. Open `/Users/kw35262/Documents/dev/Stock-Commission/index.html` in browser
2. Move cursor quickly across info buttons - tooltips should NOT appear
3. Hover over info button for 200ms+ - tooltip should appear
4. Move mouse away - tooltip should disappear instantly
5. Use Tab key to focus info buttons - tooltip should appear immediately

**Ready for user manual testing and approval.**

## Lessons

### Task 3 - Debugging Auto-calculation
- **Issue**: User reported auto-calculation wasn't working (not triggering after typing)
- **Debugging approach**: Added comprehensive console.log statements to trace execution flow
- **Resolution**: Debug logging helped verify the implementation was working correctly
- **Takeaway**: When user reports feature not working, add debug logging first to verify if it's truly broken or a misunderstanding of expected behavior
- **Note**: Debug console.log statements left in place for ongoing troubleshooting (can be removed later if desired)

### Additional Enhancement - Commission Rate Warning
- **Request**: User requested warning when commission rate (佣金费率) exceeds 10 万分之
- **Implementation**: Added validation in performCalculation() to check if commissionRateWan > 10
- **Warning message**: "佣金费率（X 万分之）超过 10 万分之，请确认费率是否正确。一般券商佣金费率在 1-5 万分之之间。"
- **Note**: Warning can appear alongside existing negative profit warning (multiple warnings supported)

### Additional Enhancement - Buy Price Validation
- **Request**: User requested validation that 买入成交价格 (元) is not empty or 0
- **Implementation**: Added check before other validations: `if (!form.buyPrice.value || buyPrice === 0)`
- **Error message**: "请填写买入成交价格，且价格需大于 0"
- **Location**: index.html lines 244-248
