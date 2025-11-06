# Festival Map - Testing Guide

## Unit Tests

We have a comprehensive test suite to prevent regressions and catch issues before they affect users.

### Running the Tests

1. Open `tests.html` in your browser
2. The tests will run automatically and display results
3. All tests should show as passing (✓)

### Test Coverage

#### 1. Geocode Cache Tests
- Validates that the geocodeCache initializes correctly
- Tests loading from localStorage
- Ensures proper error handling for corrupted data

#### 2. Coordinate Overrides Tests
- Validates that coordinateOverrides initialize correctly
- Tests graceful handling of invalid JSON in localStorage

#### 3. Function Definitions Tests
- Verifies all critical functions are defined
- Tests parameter validation in cacheGeocodingResult()
- Validates error logging function

#### 4. Data Structures Tests
- Validates corrections.json structure
- Tests geocoding cache entry format
- Ensures all required fields are present

#### 5. GitHub Integration Tests
- Validates GitHub API URL formatting
- Tests timeout configuration (5 seconds)
- Ensures proper fallback behavior

#### 6. Error Handling Tests
- Tests graceful degradation with invalid data
- Validates recovery from missing localStorage
- Tests try-catch blocks work properly

### Adding New Tests

To add new tests, edit `tests.html`:

```javascript
runner.describe('New Feature', () => {
  runner.it('should do something', () => {
    // Test logic here
    runner.expect(value).toBe(expected);
  });
});
```

### Key Test Scenarios

#### Scenario 1: App Loads Without GitHub
✓ If GitHub is unreachable, app still loads from localStorage cache
✓ Timeout is 5 seconds to prevent hanging
✓ Users see all cached data immediately

#### Scenario 2: Corrupt localStorage Data
✓ App doesn't crash if localStorage has invalid JSON
✓ Falls back to empty objects safely
✓ User gets fresh data from API

#### Scenario 3: Multiple Events at Same Location
✓ Markers properly group events
✓ Popup navigation works correctly
✓ List item highlighting works for all events

#### Scenario 4: Search and Filtering
✓ Search works with Hebrew and English text
✓ Date filters work correctly
✓ Availability filters update properly

#### Scenario 5: Event Details
✓ All event fields display correctly
✓ Directions button opens maps properly
✓ Favorite button persists across sessions

### Manual Testing Checklist

Before pushing to production, manually test:

- [ ] Map loads and centers on Pardes Hana
- [ ] Events from API appear on map
- [ ] Event list shows all events
- [ ] Search filters events correctly
- [ ] Date filter works
- [ ] Clicking event pans to location and shows details
- [ ] Favorite button works and persists
- [ ] Directions button opens maps app
- [ ] "What's next" shows nearby events
- [ ] Time-based highlighting (green/yellow) works
- [ ] Background geocoding progress shows
- [ ] App recovers when API is slow/offline
- [ ] Coordinates can be overridden manually
- [ ] Corrections persist in localStorage

### Integration Testing

To test the full integration:

1. Open `index.html` in browser
2. Open browser DevTools (F12)
3. Check Console tab for errors
4. Verify all events load and display
5. Test all user interactions
6. Check Network tab for failed requests
7. Clear localStorage and test recovery

### Debugging Tips

If tests fail:

1. Check browser console for error messages
2. Look at the error details shown in the test output
3. Verify localStorage content: `console.log(localStorage)`
4. Test individual functions in browser console
5. Check that all required HTML elements exist

### Common Issues

**Issue**: "geocodeCache is not defined"
- **Solution**: Ensure localStorage initialization is inside the script, not commented out

**Issue**: "Map not loading"
- **Solution**: Check that the #map element exists in HTML and Leaflet JS is loaded

**Issue**: "Events not appearing"
- **Solution**: Check that API endpoint is accessible and returning valid JSON

**Issue**: "GitHub corrections not loading"
- **Solution**: Normal - app falls back to localStorage after 5 second timeout if GitHub is unavailable

### Best Practices

1. Always run tests before pushing changes
2. Add a test for any bug you fix
3. Test with slow network (DevTools → Network tab → Slow 3G)
4. Clear localStorage periodically to test recovery
5. Test on mobile devices for touch interactions
6. Monitor browser console for warnings
