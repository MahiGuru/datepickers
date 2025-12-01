# Flatpickr DateTime Picker for AngularJS (1.x)

A comprehensive step-by-step guide for integrating Flatpickr date/time picker in AngularJS applications.

---

## Table of Contents

1. [Installation](#1-installation)
2. [Creating the Flatpickr Directive](#2-creating-the-flatpickr-directive)
3. [Date Picker (Date Only)](#3-date-picker-date-only)
4. [DateTime Picker - 24 Hour Format](#4-datetime-picker---24-hour-format)
5. [DateTime Picker - 12 Hour Format](#5-datetime-picker---12-hour-format)
6. [DateTime Picker with Timezone](#6-datetime-picker-with-timezone)
7. [Time Only Picker](#7-time-only-picker)
8. [Date Range Picker](#8-date-range-picker)
9. [Form Validation](#9-form-validation)
10. [Complete Working Example](#10-complete-working-example)

---

## 1. Installation

### Step 1: Include Flatpickr via CDN or npm

**Using CDN:**

```html
<!-- Flatpickr CSS -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
<!-- Optional theme -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/themes/material_blue.css">

<!-- AngularJS -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.8.2/angular.min.js"></script>

<!-- Flatpickr JS -->
<script src="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.js"></script>
```

**Using npm:**

```bash
npm install flatpickr
npm install angular@1.8.2
```

### Step 2: Create your AngularJS module

```javascript
var app = angular.module('myApp', []);
```

---

## 2. Creating the Flatpickr Directive

### Basic Directive (Copy this to your project)

```javascript
/**
 * Flatpickr Directive for AngularJS
 * Supports date, datetime, time, and range pickers
 */
app.directive('flatpickr', ['$timeout', function($timeout) {
    return {
        restrict: 'A',
        require: 'ngModel',
        scope: {
            fpOptions: '=',       // Flatpickr configuration object
            fpOnChange: '&',      // Callback when date changes
            fpOnOpen: '&',        // Callback when picker opens
            fpOnClose: '&'        // Callback when picker closes
        },
        link: function(scope, element, attrs, ngModel) {
            // Build configuration
            var config = angular.copy(scope.fpOptions) || {};

            // onChange handler - sync with ng-model
            config.onChange = function(selectedDates, dateStr, instance) {
                $timeout(function() {
                    ngModel.$setViewValue(dateStr);
                    if (scope.fpOnChange) {
                        scope.fpOnChange({
                            dates: selectedDates,
                            dateStr: dateStr,
                            instance: instance
                        });
                    }
                });
            };

            // onOpen handler
            if (scope.fpOnOpen) {
                config.onOpen = function(selectedDates, dateStr, instance) {
                    $timeout(function() {
                        scope.fpOnOpen({
                            dates: selectedDates,
                            dateStr: dateStr,
                            instance: instance
                        });
                    });
                };
            }

            // onClose handler
            if (scope.fpOnClose) {
                config.onClose = function(selectedDates, dateStr, instance) {
                    $timeout(function() {
                        scope.fpOnClose({
                            dates: selectedDates,
                            dateStr: dateStr,
                            instance: instance
                        });
                    });
                };
            }

            // Initialize Flatpickr
            var fp = flatpickr(element[0], config);

            // Store instance on element for external access
            element.data('flatpickr', fp);

            // Watch for external model changes
            ngModel.$render = function() {
                if (ngModel.$viewValue) {
                    fp.setDate(ngModel.$viewValue, false);
                } else {
                    fp.clear();
                }
            };

            // Watch for options changes
            scope.$watch('fpOptions', function(newVal, oldVal) {
                if (newVal !== oldVal && newVal) {
                    angular.forEach(newVal, function(value, key) {
                        if (key !== 'onChange' && key !== 'onOpen' && key !== 'onClose') {
                            fp.set(key, value);
                        }
                    });
                }
            }, true);

            // Cleanup on destroy
            scope.$on('$destroy', function() {
                if (fp) {
                    fp.destroy();
                }
            });
        }
    };
}]);
```

---

## 3. Date Picker (Date Only)

### Example 1: Simple Date Picker (No Time)

```html
<div ng-app="myApp" ng-controller="DateController">
    <div class="form-group">
        <label>Select Date</label>
        <input type="text"
               flatpickr
               ng-model="selectedDate"
               fp-options="dateOptions"
               placeholder="Choose a date...">
        <p>Selected: {{ selectedDate }}</p>
    </div>
</div>

<script>
app.controller('DateController', ['$scope', function($scope) {
    $scope.selectedDate = '';

    $scope.dateOptions = {
        dateFormat: 'Y-m-d',
        altInput: true,
        altFormat: 'F j, Y'
    };
}]);
</script>
```

### Example 2: Date Picker with Min/Max Dates

```html
<div ng-controller="DateRangeController">
    <div class="form-group">
        <label>Select Date (Next 30 days)</label>
        <input type="text"
               flatpickr
               ng-model="selectedDate"
               fp-options="dateOptions"
               placeholder="Choose a date...">
    </div>
</div>

<script>
app.controller('DateRangeController', ['$scope', function($scope) {
    $scope.selectedDate = '';

    var today = new Date();
    var maxDate = new Date();
    maxDate.setDate(maxDate.getDate() + 30);

    $scope.dateOptions = {
        dateFormat: 'Y-m-d',
        minDate: today,
        maxDate: maxDate
    };
}]);
</script>
```

### Example 3: Multiple Dates Selection

```html
<div ng-controller="MultiDateController">
    <div class="form-group">
        <label>Select Multiple Dates</label>
        <input type="text"
               flatpickr
               ng-model="multipleDates"
               fp-options="multiOptions"
               placeholder="Choose dates...">
        <p>Selected: {{ multipleDates }}</p>
    </div>
</div>

<script>
app.controller('MultiDateController', ['$scope', function($scope) {
    $scope.multipleDates = '';

    $scope.multiOptions = {
        mode: 'multiple',
        dateFormat: 'Y-m-d',
        conjunction: ', '
    };
}]);
</script>
```

---

## 4. DateTime Picker - 24 Hour Format

### Example 1: Basic DateTime (24-hour)

```html
<div ng-controller="DateTime24HrController">
    <div class="form-group">
        <label>Select Date and Time (24-hour format)</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               placeholder="2024-01-15 14:30">
        <p>Selected: {{ selectedDateTime }}</p>
    </div>
</div>

<script>
app.controller('DateTime24HrController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true  // Key setting for 24-hour format
    };
}]);
</script>
```

**Key Configuration:**
- `time_24hr: true` - Enables 24-hour format
- `dateFormat: 'Y-m-d H:i'` - Format: 2024-01-15 14:30

### Example 2: DateTime 24-hour with Seconds

```html
<div ng-controller="DateTime24HrSecondsController">
    <div class="form-group">
        <label>DateTime with Seconds (24-hour)</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               placeholder="2024-01-15 14:30:45">
    </div>
</div>

<script>
app.controller('DateTime24HrSecondsController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        enableSeconds: true,
        dateFormat: 'Y-m-d H:i:S',
        time_24hr: true
    };
}]);
</script>
```

### Example 3: DateTime 24-hour with 15-minute Increments

```html
<div ng-controller="DateTime24HrIncrementController">
    <div class="form-group">
        <label>DateTime (24-hour, 15-min increments)</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               placeholder="Choose date and time...">
    </div>
</div>

<script>
app.controller('DateTime24HrIncrementController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        minuteIncrement: 15,
        defaultHour: 9,
        defaultMinute: 0
    };
}]);
</script>
```

---

## 5. DateTime Picker - 12 Hour Format

### Example 1: Basic DateTime (12-hour with AM/PM)

```html
<div ng-controller="DateTime12HrController">
    <div class="form-group">
        <label>Select Date and Time (12-hour AM/PM)</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               placeholder="2024-01-15 02:30 PM">
        <p>Selected: {{ selectedDateTime }}</p>
    </div>
</div>

<script>
app.controller('DateTime12HrController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d h:i K',
        time_24hr: false  // Key setting for 12-hour format
    };
}]);
</script>
```

**Key Configuration:**
- `time_24hr: false` - Enables 12-hour format with AM/PM
- `dateFormat: 'Y-m-d h:i K'` - `h` for 12-hour, `K` for AM/PM

### Example 2: DateTime 12-hour with Friendly Display

```html
<div ng-controller="DateTime12HrFriendlyController">
    <div class="form-group">
        <label>DateTime with Friendly Display</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               placeholder="January 15, 2024 at 2:30 PM">
    </div>
</div>

<script>
app.controller('DateTime12HrFriendlyController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d h:i K',
        time_24hr: false,
        altInput: true,
        altFormat: 'F j, Y at h:i K'  // Display: January 15, 2024 at 2:30 PM
    };
}]);
</script>
```

### Example 3: DateTime 12-hour with Seconds

```html
<div ng-controller="DateTime12HrSecondsController">
    <div class="form-group">
        <label>DateTime 12-hour with Seconds</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               placeholder="2024-01-15 02:30:45 PM">
    </div>
</div>

<script>
app.controller('DateTime12HrSecondsController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        enableSeconds: true,
        dateFormat: 'Y-m-d h:i:S K',
        time_24hr: false
    };
}]);
</script>
```

---

## 6. DateTime Picker with Timezone

> **Note:** Flatpickr doesn't have built-in timezone support. Use these patterns to handle timezones in your application.

### Example 1: DateTime with Timezone Selector

```html
<div ng-controller="DateTimeTimezoneController">
    <div class="form-group">
        <label>Select Date and Time with Timezone</label>
        <div class="datetime-timezone-wrapper">
            <input type="text"
                   flatpickr
                   ng-model="selectedDateTime"
                   fp-options="dateTimeOptions"
                   fp-on-change="onDateTimeChange(dates, dateStr)"
                   placeholder="Choose date and time...">
            <select ng-model="selectedTimezone" ng-change="onTimezoneChange()">
                <option value="UTC">UTC</option>
                <option value="America/New_York">Eastern Time (ET)</option>
                <option value="America/Chicago">Central Time (CT)</option>
                <option value="America/Denver">Mountain Time (MT)</option>
                <option value="America/Los_Angeles">Pacific Time (PT)</option>
                <option value="Europe/London">London (GMT)</option>
                <option value="Europe/Paris">Paris (CET)</option>
                <option value="Asia/Tokyo">Tokyo (JST)</option>
                <option value="Asia/Kolkata">India (IST)</option>
                <option value="Australia/Sydney">Sydney (AEDT)</option>
            </select>
        </div>
        <div class="timezone-display">
            <p><strong>Local:</strong> {{ selectedDateTime }}</p>
            <p><strong>UTC:</strong> {{ utcDateTime }}</p>
            <p><strong>{{ selectedTimezone }}:</strong> {{ displayDateTime }}</p>
        </div>
    </div>
</div>

<script>
app.controller('DateTimeTimezoneController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';
    $scope.selectedTimezone = 'UTC';
    $scope.utcDateTime = '';
    $scope.displayDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true
    };

    $scope.onDateTimeChange = function(dates, dateStr) {
        if (dates[0]) {
            var date = dates[0];
            $scope.utcDateTime = date.toISOString();
            $scope.updateDisplayDateTime(date);
        }
    };

    $scope.onTimezoneChange = function() {
        if ($scope.selectedDateTime) {
            $scope.updateDisplayDateTime(new Date($scope.selectedDateTime));
        }
    };

    $scope.updateDisplayDateTime = function(date) {
        $scope.displayDateTime = date.toLocaleString('en-US', {
            timeZone: $scope.selectedTimezone,
            year: 'numeric',
            month: '2-digit',
            day: '2-digit',
            hour: '2-digit',
            minute: '2-digit',
            hour12: false
        });
    };
}]);
</script>
```

### Example 2: DateTime with Timezone Offset Display

```html
<div ng-controller="DateTimeOffsetController">
    <div class="form-group">
        <label>DateTime with Timezone Offset</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               fp-on-change="onDateTimeChange(dates, dateStr)"
               placeholder="Choose date and time...">
        <div class="timezone-info">
            <p><strong>ISO 8601:</strong> {{ isoString }}</p>
            <p><strong>With Offset:</strong> {{ dateWithOffset }}</p>
            <p><strong>Timezone:</strong> {{ timezoneAbbr }} ({{ timezoneOffset }})</p>
        </div>
    </div>
</div>

<script>
app.controller('DateTimeOffsetController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';
    $scope.isoString = '';
    $scope.dateWithOffset = '';
    $scope.timezoneOffset = '';
    $scope.timezoneAbbr = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true
    };

    $scope.onDateTimeChange = function(dates, dateStr) {
        if (dates[0]) {
            var date = dates[0];
            $scope.isoString = date.toISOString();

            // Calculate timezone offset
            var offset = -date.getTimezoneOffset();
            var hours = Math.floor(Math.abs(offset) / 60);
            var minutes = Math.abs(offset) % 60;
            var sign = offset >= 0 ? '+' : '-';
            $scope.timezoneOffset = sign +
                hours.toString().padStart(2, '0') + ':' +
                minutes.toString().padStart(2, '0');

            // Format with offset
            var year = date.getFullYear();
            var month = (date.getMonth() + 1).toString().padStart(2, '0');
            var day = date.getDate().toString().padStart(2, '0');
            var hour = date.getHours().toString().padStart(2, '0');
            var minute = date.getMinutes().toString().padStart(2, '0');
            $scope.dateWithOffset = year + '-' + month + '-' + day + 'T' +
                                    hour + ':' + minute + ':00' + $scope.timezoneOffset;

            // Get timezone abbreviation
            var parts = Intl.DateTimeFormat('en', { timeZoneName: 'short' }).formatToParts(date);
            var tzPart = parts.find(function(part) { return part.type === 'timeZoneName'; });
            $scope.timezoneAbbr = tzPart ? tzPart.value : '';
        }
    };
}]);
</script>
```

### Example 3: UTC DateTime Picker

```html
<div ng-controller="DateTimeUTCController">
    <div class="form-group">
        <label>Select DateTime (Stored as UTC)</label>
        <input type="text"
               flatpickr
               ng-model="localDateTime"
               fp-options="dateTimeOptions"
               fp-on-change="convertToUTC(dates, dateStr)"
               placeholder="Choose date and time...">
        <div class="utc-display">
            <p><strong>Local Time:</strong> {{ localDateTime }}</p>
            <p><strong>UTC Time:</strong> {{ utcDateTime }}</p>
            <p><strong>ISO String:</strong> {{ isoString }}</p>
        </div>
    </div>
</div>

<script>
app.controller('DateTimeUTCController', ['$scope', function($scope) {
    $scope.localDateTime = '';
    $scope.utcDateTime = '';
    $scope.isoString = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true
    };

    $scope.convertToUTC = function(dates, dateStr) {
        if (dates[0]) {
            var localDate = dates[0];
            $scope.utcDateTime = localDate.toUTCString();
            $scope.isoString = localDate.toISOString();
        }
    };
}]);
</script>
```

### Example 4: Multi-Timezone Display

```html
<div ng-controller="MultiTimezoneController">
    <div class="form-group">
        <label>Select DateTime to see in Multiple Timezones</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               fp-on-change="updateAllTimezones(dates, dateStr)"
               placeholder="Choose date and time...">

        <div class="timezone-grid" ng-if="selectedDateTime">
            <div class="timezone-card" ng-repeat="tz in timezones">
                <div class="tz-name">{{ tz.name }}</div>
                <div class="tz-time">{{ tz.time }}</div>
                <div class="tz-id">{{ tz.id }}</div>
            </div>
        </div>
    </div>
</div>

<script>
app.controller('MultiTimezoneController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true
    };

    $scope.timezones = [
        { id: 'UTC', name: 'UTC', time: '' },
        { id: 'America/New_York', name: 'New York (ET)', time: '' },
        { id: 'America/Los_Angeles', name: 'Los Angeles (PT)', time: '' },
        { id: 'Europe/London', name: 'London (GMT)', time: '' },
        { id: 'Europe/Paris', name: 'Paris (CET)', time: '' },
        { id: 'Asia/Tokyo', name: 'Tokyo (JST)', time: '' },
        { id: 'Asia/Kolkata', name: 'Mumbai (IST)', time: '' },
        { id: 'Australia/Sydney', name: 'Sydney (AEDT)', time: '' }
    ];

    $scope.updateAllTimezones = function(dates, dateStr) {
        if (dates[0]) {
            var date = dates[0];
            $scope.timezones = $scope.timezones.map(function(tz) {
                return {
                    id: tz.id,
                    name: tz.name,
                    time: date.toLocaleString('en-US', {
                        timeZone: tz.id,
                        year: 'numeric',
                        month: 'short',
                        day: '2-digit',
                        hour: '2-digit',
                        minute: '2-digit',
                        hour12: false
                    })
                };
            });
        }
    };
}]);
</script>
```

---

## 7. Time Only Picker

### Example 1: Basic Time Picker

```html
<div ng-controller="TimeController">
    <div class="form-group">
        <label>Select Time</label>
        <input type="text"
               flatpickr
               ng-model="selectedTime"
               fp-options="timeOptions"
               placeholder="Choose time...">
        <p>Selected: {{ selectedTime }}</p>
    </div>
</div>

<script>
app.controller('TimeController', ['$scope', function($scope) {
    $scope.selectedTime = '';

    $scope.timeOptions = {
        enableTime: true,
        noCalendar: true,
        dateFormat: 'H:i',
        time_24hr: true
    };
}]);
</script>
```

### Example 2: Time Picker with Min/Max

```html
<div ng-controller="TimeLimitsController">
    <div class="form-group">
        <label>Business Hours (9 AM - 5 PM)</label>
        <input type="text"
               flatpickr
               ng-model="selectedTime"
               fp-options="timeOptions"
               placeholder="Choose time...">
    </div>
</div>

<script>
app.controller('TimeLimitsController', ['$scope', function($scope) {
    $scope.selectedTime = '';

    $scope.timeOptions = {
        enableTime: true,
        noCalendar: true,
        dateFormat: 'H:i',
        time_24hr: true,
        minTime: '09:00',
        maxTime: '17:00',
        minuteIncrement: 30
    };
}]);
</script>
```

### Example 3: Time Picker with 12-hour Format

```html
<div ng-controller="Time12HrController">
    <div class="form-group">
        <label>Select Time (12-hour)</label>
        <input type="text"
               flatpickr
               ng-model="selectedTime"
               fp-options="timeOptions"
               placeholder="Choose time...">
    </div>
</div>

<script>
app.controller('Time12HrController', ['$scope', function($scope) {
    $scope.selectedTime = '';

    $scope.timeOptions = {
        enableTime: true,
        noCalendar: true,
        dateFormat: 'h:i K',
        time_24hr: false,
        minuteIncrement: 15
    };
}]);
</script>
```

---

## 8. Date Range Picker

### Example 1: Basic Date Range

```html
<div ng-controller="DateRangeController">
    <div class="form-group">
        <label>Select Date Range</label>
        <input type="text"
               flatpickr
               ng-model="dateRange"
               fp-options="rangeOptions"
               placeholder="Choose date range...">
        <p>Selected: {{ dateRange }}</p>
    </div>
</div>

<script>
app.controller('DateRangeController', ['$scope', function($scope) {
    $scope.dateRange = '';

    $scope.rangeOptions = {
        mode: 'range',
        dateFormat: 'Y-m-d'
    };
}]);
</script>
```

### Example 2: DateTime Range Picker

```html
<div ng-controller="DateTimeRangeController">
    <div class="form-group">
        <label>Select DateTime Range</label>
        <input type="text"
               flatpickr
               ng-model="dateTimeRange"
               fp-options="rangeOptions"
               placeholder="Choose datetime range...">
    </div>
</div>

<script>
app.controller('DateTimeRangeController', ['$scope', function($scope) {
    $scope.dateTimeRange = '';

    $scope.rangeOptions = {
        mode: 'range',
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true
    };
}]);
</script>
```

### Example 3: Linked Start/End Date Pickers

```html
<div ng-controller="LinkedRangeController">
    <div class="form-row">
        <div class="form-group">
            <label>Start Date</label>
            <input type="text"
                   flatpickr
                   ng-model="startDate"
                   fp-options="startOptions"
                   fp-on-change="onStartChange(dates, dateStr)"
                   placeholder="Start date...">
        </div>
        <div class="form-group">
            <label>End Date</label>
            <input type="text"
                   flatpickr
                   ng-model="endDate"
                   fp-options="endOptions"
                   fp-on-change="onEndChange(dates, dateStr)"
                   placeholder="End date...">
        </div>
    </div>
    <p>Range: {{ startDate }} to {{ endDate }}</p>
</div>

<script>
app.controller('LinkedRangeController', ['$scope', function($scope) {
    $scope.startDate = '';
    $scope.endDate = '';

    $scope.startOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true
    };

    $scope.endOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true
    };

    $scope.onStartChange = function(dates, dateStr) {
        // Update end date minimum
        $scope.endOptions = angular.extend({}, $scope.endOptions, {
            minDate: dateStr
        });
    };

    $scope.onEndChange = function(dates, dateStr) {
        // Update start date maximum
        $scope.startOptions = angular.extend({}, $scope.startOptions, {
            maxDate: dateStr
        });
    };
}]);
</script>
```

---

## Advanced Configuration

### Example 1: Inline Calendar

```html
<div ng-controller="InlineController">
    <div class="form-group">
        <label>Inline DateTime Picker</label>
        <div id="inline-picker"
             flatpickr
             ng-model="selectedDateTime"
             fp-options="inlineOptions">
        </div>
        <p>Selected: {{ selectedDateTime }}</p>
    </div>
</div>

<script>
app.controller('InlineController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.inlineOptions = {
        inline: true,
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true
    };
}]);
</script>
```

### Example 2: Disable Specific Dates

```html
<div ng-controller="DisabledDatesController">
    <div class="form-group">
        <label>DateTime (Weekends Disabled)</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               placeholder="Choose date and time...">
    </div>
</div>

<script>
app.controller('DisabledDatesController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        disable: [
            function(date) {
                // Disable weekends
                return date.getDay() === 0 || date.getDay() === 6;
            }
        ]
    };
}]);
</script>
```

### Example 3: Enable Only Specific Dates

```html
<div ng-controller="EnabledDatesController">
    <div class="form-group">
        <label>DateTime (Weekdays Only)</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               placeholder="Choose date and time...">
    </div>
</div>

<script>
app.controller('EnabledDatesController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        enable: [
            function(date) {
                // Enable only weekdays
                return date.getDay() !== 0 && date.getDay() !== 6;
            }
        ]
    };
}]);
</script>
```

### Example 4: Week Numbers

```html
<div ng-controller="WeekNumbersController">
    <div class="form-group">
        <label>DateTime with Week Numbers</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               placeholder="Choose date and time...">
    </div>
</div>

<script>
app.controller('WeekNumbersController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        weekNumbers: true
    };
}]);
</script>
```

### Example 5: Custom Locale (e.g., Spanish)

```html
<!-- Include locale file -->
<script src="https://cdn.jsdelivr.net/npm/flatpickr/dist/l10n/es.js"></script>

<div ng-controller="LocaleController">
    <div class="form-group">
        <label>Fecha y Hora (Spanish)</label>
        <input type="text"
               flatpickr
               ng-model="selectedDateTime"
               fp-options="dateTimeOptions"
               placeholder="Seleccionar fecha y hora...">
    </div>
</div>

<script>
app.controller('LocaleController', ['$scope', function($scope) {
    $scope.selectedDateTime = '';

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'd/m/Y H:i',
        time_24hr: true,
        locale: flatpickr.l10ns.es
    };
}]);
</script>
```

---

## 9. Form Validation

### Example: Form with Required DateTime Fields

```html
<div ng-controller="FormController">
    <form name="bookingForm" ng-submit="submitForm()" novalidate>
        <div class="form-group">
            <label>Event Name *</label>
            <input type="text"
                   name="eventName"
                   ng-model="booking.eventName"
                   required>
            <span class="error" ng-show="bookingForm.eventName.$touched && bookingForm.eventName.$invalid">
                Event name is required
            </span>
        </div>

        <div class="form-group">
            <label>Event Date *</label>
            <input type="text"
                   name="eventDate"
                   flatpickr
                   ng-model="booking.eventDate"
                   fp-options="dateOptions"
                   ng-required="true"
                   placeholder="Select date...">
            <span class="error" ng-show="bookingForm.eventDate.$touched && !booking.eventDate">
                Event date is required
            </span>
        </div>

        <div class="form-group">
            <label>Start Time *</label>
            <input type="text"
                   name="startTime"
                   flatpickr
                   ng-model="booking.startTime"
                   fp-options="timeOptions"
                   ng-required="true"
                   placeholder="Select start time...">
        </div>

        <div class="form-group">
            <label>End Time *</label>
            <input type="text"
                   name="endTime"
                   flatpickr
                   ng-model="booking.endTime"
                   fp-options="timeOptions"
                   ng-required="true"
                   placeholder="Select end time...">
        </div>

        <div class="form-group">
            <label>Full DateTime</label>
            <input type="text"
                   name="fullDateTime"
                   flatpickr
                   ng-model="booking.fullDateTime"
                   fp-options="dateTimeOptions"
                   placeholder="Select datetime...">
        </div>

        <button type="submit"
                ng-disabled="bookingForm.$invalid || !booking.eventDate || !booking.startTime || !booking.endTime">
            Submit Booking
        </button>
    </form>

    <div class="result" ng-if="submitted">
        <h3>Booking Details:</h3>
        <pre>{{ booking | json }}</pre>
    </div>
</div>

<script>
app.controller('FormController', ['$scope', function($scope) {
    $scope.submitted = false;

    $scope.booking = {
        eventName: '',
        eventDate: '',
        startTime: '',
        endTime: '',
        fullDateTime: ''
    };

    var today = new Date();

    $scope.dateOptions = {
        dateFormat: 'Y-m-d',
        minDate: today
    };

    $scope.timeOptions = {
        enableTime: true,
        noCalendar: true,
        dateFormat: 'H:i',
        time_24hr: true,
        minuteIncrement: 15
    };

    $scope.dateTimeOptions = {
        enableTime: true,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        minDate: today
    };

    $scope.submitForm = function() {
        if ($scope.bookingForm.$valid && $scope.booking.eventDate &&
            $scope.booking.startTime && $scope.booking.endTime) {
            $scope.submitted = true;
            console.log('Form submitted:', $scope.booking);
        }
    };
}]);
</script>
```

---

## 10. Complete Working Example

### Full HTML File

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AngularJS Flatpickr DateTime Picker</title>

    <!-- Flatpickr CSS -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/themes/material_blue.css">

    <style>
        * { box-sizing: border-box; }
        body {
            font-family: 'Segoe UI', Tahoma, sans-serif;
            margin: 0;
            padding: 20px;
            background: linear-gradient(135deg, #dd1b16 0%, #b71c1c 100%);
            min-height: 100vh;
        }
        .container { max-width: 800px; margin: 0 auto; }
        h1 { color: white; text-align: center; margin-bottom: 30px; }
        .card {
            background: white;
            border-radius: 12px;
            padding: 25px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.2);
            margin-bottom: 20px;
        }
        .card h2 {
            margin-top: 0;
            padding-bottom: 15px;
            border-bottom: 2px solid #eee;
            color: #333;
        }
        .form-row { display: flex; gap: 15px; }
        .form-row .form-group { flex: 1; }
        .form-group { margin-bottom: 20px; }
        .form-group label {
            display: block;
            margin-bottom: 8px;
            font-weight: 600;
            color: #333;
        }
        .form-group input {
            width: 100%;
            padding: 12px;
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            font-size: 14px;
            transition: border-color 0.3s;
        }
        .form-group input:focus {
            outline: none;
            border-color: #dd1b16;
        }
        .output-panel {
            background: #f8f9fa;
            padding: 20px;
            border-radius: 8px;
            border-left: 4px solid #dd1b16;
        }
        .output-panel h3 { margin-top: 0; color: #dd1b16; }
        .output-panel p {
            margin: 8px 0;
            font-family: monospace;
            background: white;
            padding: 8px 12px;
            border-radius: 4px;
        }
        .output-panel strong {
            color: #666;
            min-width: 120px;
            display: inline-block;
        }
    </style>
</head>
<body ng-app="datePickerApp">
    <div class="container">
        <h1>AngularJS Flatpickr DateTime Picker</h1>

        <div class="card" ng-controller="MainController">
            <h2>DateTime Picker Examples</h2>

            <div class="form-row">
                <div class="form-group">
                    <label>Date Picker</label>
                    <input type="text"
                           flatpickr
                           ng-model="date"
                           fp-options="dateOpts"
                           placeholder="Select date...">
                </div>
                <div class="form-group">
                    <label>DateTime Picker</label>
                    <input type="text"
                           flatpickr
                           ng-model="dateTime"
                           fp-options="dateTimeOpts"
                           placeholder="Select datetime...">
                </div>
            </div>

            <div class="form-row">
                <div class="form-group">
                    <label>Time Only</label>
                    <input type="text"
                           flatpickr
                           ng-model="time"
                           fp-options="timeOpts"
                           placeholder="Select time...">
                </div>
                <div class="form-group">
                    <label>Date Range</label>
                    <input type="text"
                           flatpickr
                           ng-model="range"
                           fp-options="rangeOpts"
                           placeholder="Select range...">
                </div>
            </div>

            <div class="form-row">
                <div class="form-group">
                    <label>DateTime with Seconds</label>
                    <input type="text"
                           flatpickr
                           ng-model="dateTimeSeconds"
                           fp-options="dateTimeSecondsOpts"
                           placeholder="Select datetime...">
                </div>
                <div class="form-group">
                    <label>Multiple Dates</label>
                    <input type="text"
                           flatpickr
                           ng-model="multipleDates"
                           fp-options="multiOpts"
                           placeholder="Select multiple...">
                </div>
            </div>

            <div class="output-panel">
                <h3>Selected Values (ng-model)</h3>
                <p><strong>Date:</strong> {{ date || 'Not selected' }}</p>
                <p><strong>DateTime:</strong> {{ dateTime || 'Not selected' }}</p>
                <p><strong>Time:</strong> {{ time || 'Not selected' }}</p>
                <p><strong>Range:</strong> {{ range || 'Not selected' }}</p>
                <p><strong>DateTime+Sec:</strong> {{ dateTimeSeconds || 'Not selected' }}</p>
                <p><strong>Multiple:</strong> {{ multipleDates || 'Not selected' }}</p>
            </div>
        </div>
    </div>

    <!-- AngularJS -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.8.2/angular.min.js"></script>
    <!-- Flatpickr -->
    <script src="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.js"></script>

    <script>
        var app = angular.module('datePickerApp', []);

        // Flatpickr Directive
        app.directive('flatpickr', ['$timeout', function($timeout) {
            return {
                restrict: 'A',
                require: 'ngModel',
                scope: {
                    fpOptions: '=',
                    fpOnChange: '&'
                },
                link: function(scope, element, attrs, ngModel) {
                    var config = angular.copy(scope.fpOptions) || {};

                    config.onChange = function(selectedDates, dateStr) {
                        $timeout(function() {
                            ngModel.$setViewValue(dateStr);
                            if (scope.fpOnChange) {
                                scope.fpOnChange({
                                    dates: selectedDates,
                                    dateStr: dateStr
                                });
                            }
                        });
                    };

                    var fp = flatpickr(element[0], config);

                    ngModel.$render = function() {
                        if (ngModel.$viewValue) {
                            fp.setDate(ngModel.$viewValue, false);
                        }
                    };

                    scope.$on('$destroy', function() {
                        fp.destroy();
                    });
                }
            };
        }]);

        // Main Controller
        app.controller('MainController', ['$scope', function($scope) {
            $scope.date = '';
            $scope.dateTime = '';
            $scope.time = '';
            $scope.range = '';
            $scope.dateTimeSeconds = '';
            $scope.multipleDates = '';

            $scope.dateOpts = {
                dateFormat: 'Y-m-d'
            };

            $scope.dateTimeOpts = {
                enableTime: true,
                dateFormat: 'Y-m-d H:i',
                time_24hr: true,
                minuteIncrement: 5
            };

            $scope.timeOpts = {
                enableTime: true,
                noCalendar: true,
                dateFormat: 'H:i',
                time_24hr: true,
                minuteIncrement: 15
            };

            $scope.rangeOpts = {
                mode: 'range',
                dateFormat: 'Y-m-d'
            };

            $scope.dateTimeSecondsOpts = {
                enableTime: true,
                enableSeconds: true,
                dateFormat: 'Y-m-d H:i:S',
                time_24hr: true
            };

            $scope.multiOpts = {
                mode: 'multiple',
                dateFormat: 'Y-m-d'
            };
        }]);
    </script>
</body>
</html>
```

---

## Quick Reference: Common Configuration Options

| Option | Type | Description |
|--------|------|-------------|
| `enableTime` | boolean | Enable time picker |
| `noCalendar` | boolean | Time picker only |
| `dateFormat` | string | Date format string |
| `time_24hr` | boolean | Use 24-hour time |
| `minuteIncrement` | number | Minute step increment |
| `enableSeconds` | boolean | Show seconds |
| `defaultHour` | number | Default hour |
| `defaultMinute` | number | Default minute |
| `minDate` | Date/string | Minimum selectable date |
| `maxDate` | Date/string | Maximum selectable date |
| `minTime` | string | Minimum time (HH:mm) |
| `maxTime` | string | Maximum time (HH:mm) |
| `mode` | string | 'single', 'multiple', 'range' |
| `inline` | boolean | Show inline calendar |
| `altInput` | boolean | Use alternate input |
| `altFormat` | string | Alternate display format |
| `weekNumbers` | boolean | Show week numbers |
| `disable` | array | Dates/functions to disable |
| `enable` | array | Dates/functions to enable |

---

## Date Format Tokens

| Token | Description | Example |
|-------|-------------|---------|
| `Y` | 4-digit year | 2024 |
| `y` | 2-digit year | 24 |
| `m` | Month (padded) | 01-12 |
| `n` | Month (no padding) | 1-12 |
| `d` | Day (padded) | 01-31 |
| `j` | Day (no padding) | 1-31 |
| `H` | Hour (24h, padded) | 00-23 |
| `h` | Hour (12h, padded) | 01-12 |
| `i` | Minutes (padded) | 00-59 |
| `S` | Seconds (padded) | 00-59 |
| `K` | AM/PM | AM or PM |
| `F` | Full month name | January |
| `M` | Short month name | Jan |
| `l` | Full day name | Monday |
| `D` | Short day name | Mon |
