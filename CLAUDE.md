# CLAUDE.md - Meeting-Ticker Codebase Guide

This document provides essential context for AI assistants working with this codebase.

## Project Overview

**Meeting-Ticker** is a JavaScript web application that calculates and displays the real-time cost of meetings. It helps teams visualize how much money is being "spent" during meetings by tracking attendees, hourly rates, and elapsed time with an animated odometer display.

### Key Features
- Real-time cost calculation with animated odometer display
- Multi-currency support (USD, EUR, GBP, JPY, SEK) with automatic locale detection
- Meeting start time tracking
- Form validation

## Directory Structure

```
meeting-ticker/
├── index.html              # Main HTML entry point (Spanish/KENSO branded)
├── src/
│   └── meeting-ticker.coffee   # PRIMARY SOURCE - CoffeeScript (edit this!)
├── js/
│   ├── meeting-ticker.js       # Compiled output (auto-generated)
│   ├── jquery-1.3.2.min.js     # jQuery framework
│   ├── jquery-ui-1.7.2.core.min.js
│   ├── jquery.clockpick.1.2.1.js
│   ├── jquery.validate.min.js
│   ├── jquery.watermark.js
│   └── odometer/
│       └── jquery.odometer.js  # Animated number display
├── css/
│   ├── style.css               # Main styles
│   ├── odometer.css
│   └── clockpick.css
├── spec/javascripts/
│   ├── meeting_ticker_spec.js  # Jasmine test suite
│   ├── fixtures/ticker.html    # Test HTML fixture
│   └── helpers/jasmine-jquery-1.1.3.js
├── lib/tasks/
│   ├── jasmine.rake            # Test automation
│   └── coffee.rake             # CoffeeScript compilation
├── Rakefile                    # Build automation
├── Gemfile                     # Ruby dependencies
└── img/                        # Assets (KENSO logo)
```

## Build Commands

```bash
# Run tests (default task)
rake jasmine

# Watch and compile CoffeeScript automatically
rake coffee:compiler
```

## Development Workflow

1. **Edit source**: Modify `src/meeting-ticker.coffee` (this is the authoritative source)
2. **Compile**: Run `rake coffee:compiler` to watch and auto-compile to `js/meeting-ticker.js`
3. **Test**: Run `rake jasmine` to execute the test suite
4. **Manual test**: Open `index.html` in a browser

## Architecture

### Key Classes (in `src/meeting-ticker.coffee`)

**MeetingTicker** - Main application controller
- `start()` - Begin tracking meeting cost
- `stop()` - Stop tracking
- `cost()` - Calculate current meeting cost
- `hourlyRate(rate)` - Get/set hourly rate
- `attendeeCount(count)` - Get/set attendee count
- `startTime(time)` - Get/set start time
- `currency(newCurrency)` - Get/set currency
- `valid()` - Validate form

**Time** - Time manipulation
- Constructor accepts: Date objects, milliseconds, "HH:MM" strings, or Time instances
- `secondsSince(past)` - Calculate elapsed seconds
- `toString()` - Format as "HH:MM"

**Locale** - Auto-detect currency from browser locale
- `currency()` - Return currency symbol based on language code

### jQuery Plugin Pattern

The application is exposed as a jQuery plugin:
```javascript
$("form.ticker").meetingTicker({
  displaySelector: "#display"
});
```

### DOM Integration Points

```
#form              - Main form container
form.ticker        - Input form
form.stop          - Stop button form
#display           - Results display (hidden until started)
.odometer          - Cost display element
input[name="attendees"]    - Number of attendees
input[name="hourly_rate"]  - Hourly cost per person
input[name="start_time"]   - Meeting start time
select[name="units"]       - Currency selector
```

## Testing

Tests use **Jasmine 1.1.2** with jasmine-jquery extensions.

- Test file: `spec/javascripts/meeting_ticker_spec.js`
- Fixture: `spec/javascripts/fixtures/ticker.html`
- Run with: `rake jasmine`

### Test Coverage
- Plugin installation
- Form initialization and validation
- Property getters/setters
- Cost calculations (hourly burn rate, per-second burn, elapsed time)
- Event handling (form submit, stop, currency change)
- UI state management
- Locale/currency detection

## Code Conventions

### CoffeeScript Style
- Class-based OOP pattern
- Fat arrow (`=>`) for callbacks to preserve `this`
- Property methods act as getter/setter based on argument presence

### Validation Rules
```coffeescript
attendees:   { required: true, min: 1, number: true }
hourly_rate: { required: true, number: true, min: 0.01 }
start_time:  "required"
```

### Constants
- `UPDATE_INTERVAL = 125` - Odometer updates every 125ms
- `SECONDS_PER_HOUR = 3600`

## Environment Setup

```bash
# Ruby version (via RVM)
rvm use 1.9.3

# Install dependencies
bundle install

# Import gem set
rvm gemset import meeting-ticker.gems
```

## Important Notes

- **IE not supported** - Per README, IE support is not maintained
- **CoffeeScript is the source of truth** - Never edit `js/meeting-ticker.js` directly
- **Spanish translation** - The index.html is currently in Spanish with KENSO branding
- **Static deployment** - No server required; just serve static files
