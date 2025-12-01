# Flatpickr DateTime Picker for Angular 19

A comprehensive step-by-step guide for integrating Flatpickr date/time picker in modern Angular applications.

---

## Table of Contents

1. [Installation](#1-installation)
2. [Basic Setup](#2-basic-setup)
3. [Date Picker (Date Only)](#3-date-picker-date-only)
4. [DateTime Picker - 24 Hour Format](#4-datetime-picker---24-hour-format)
5. [DateTime Picker - 12 Hour Format](#5-datetime-picker---12-hour-format)
6. [DateTime Picker with Timezone](#6-datetime-picker-with-timezone)
7. [Time Only Picker](#7-time-only-picker)
8. [Date Range Picker](#8-date-range-picker)
9. [Reactive Forms Integration](#9-reactive-forms-integration)
10. [Template-Driven Forms](#10-template-driven-forms)
11. [Advanced Configuration](#11-advanced-configuration)

---

## 1. Installation

### Step 1: Install Flatpickr and Angular wrapper

```bash
# Install flatpickr core library
npm install flatpickr

# Install Angular wrapper (recommended)
npm install angularx-flatpickr
```

### Step 2: Add styles to angular.json

```json
{
  "projects": {
    "your-app": {
      "architect": {
        "build": {
          "options": {
            "styles": [
              "node_modules/flatpickr/dist/flatpickr.min.css",
              "node_modules/flatpickr/dist/themes/material_blue.css",
              "src/styles.css"
            ]
          }
        }
      }
    }
  }
}
```

---

## 2. Basic Setup

### Step 1: Import the module in your standalone component or module

**For Standalone Components (Angular 19):**

> **Note:** In Angular 19, components are standalone by default. You no longer need to specify `standalone: true`.

```typescript
// app.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-root',
  imports: [FormsModule, FlatpickrModule],
  templateUrl: './app.component.html'
})
export class AppComponent {
  selectedDate: Date | null = null;
}
```

**For NgModule-based apps:**

```typescript
// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule, ReactiveFormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@NgModule({
  imports: [
    BrowserModule,
    FormsModule,
    ReactiveFormsModule,
    FlatpickrModule.forRoot()
  ],
  declarations: [AppComponent],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

---

## 3. Date Picker (Date Only)

### Example 1: Basic Date Picker (No Time)

```typescript
// date-picker.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';
import { DatePipe } from '@angular/common';

@Component({
  selector: 'app-date-picker',
  imports: [FormsModule, FlatpickrModule, DatePipe],
  template: `
    <div class="form-group">
      <label>Select Date</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDate"
        [altInput]="true"
        [altFormat]="'F j, Y'"
        [dateFormat]="'Y-m-d'"
        placeholder="Choose a date..."
        class="form-control"
      />
      <p>Selected: {{ selectedDate | date:'fullDate' }}</p>
    </div>
  `
})
export class DatePickerComponent {
  selectedDate: Date | null = null;
}
```

### Example 2: Date Picker with Min/Max Dates

```typescript
// date-range-limits.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-date-range-limits',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Select Date (Next 30 days only)</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDate"
        [minDate]="today"
        [maxDate]="maxDate"
        [dateFormat]="'Y-m-d'"
        placeholder="Choose a date..."
        class="form-control"
      />
    </div>
  `
})
export class DateRangeLimitsComponent {
  selectedDate: Date | null = null;
  today = new Date();
  maxDate = new Date(Date.now() + 30 * 24 * 60 * 60 * 1000);
}
```

### Example 3: Multiple Dates Selection

```typescript
// multiple-dates.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-multiple-dates',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Select Multiple Dates</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDates"
        [mode]="'multiple'"
        [dateFormat]="'Y-m-d'"
        placeholder="Choose multiple dates..."
        class="form-control"
      />
      <p>Selected: {{ selectedDates }}</p>
    </div>
  `
})
export class MultipleDatesComponent {
  selectedDates: string = '';
}
```

---

## 4. DateTime Picker - 24 Hour Format

### Example 1: Basic DateTime (24-hour)

```typescript
// datetime-24hr.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-24hr',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Select Date and Time (24-hour format)</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [dateFormat]="'Y-m-d H:i'"
        [time_24hr]="true"
        placeholder="Choose date and time..."
        class="form-control"
      />
      <p>Selected: {{ selectedDateTime }}</p>
    </div>
  `
})
export class DateTime24HrComponent {
  selectedDateTime: Date | null = null;
}
```

**Key Configuration:**
- `[time_24hr]="true"` - Enables 24-hour format
- `[dateFormat]="'Y-m-d H:i'"` - Format: 2024-01-15 14:30

### Example 2: DateTime 24-hour with Seconds

```typescript
// datetime-24hr-seconds.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-24hr-seconds',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>DateTime with Seconds (24-hour)</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [enableSeconds]="true"
        [dateFormat]="'Y-m-d H:i:S'"
        [time_24hr]="true"
        placeholder="2024-01-15 14:30:45"
        class="form-control"
      />
    </div>
  `
})
export class DateTime24HrSecondsComponent {
  selectedDateTime: Date | null = null;
}
```

### Example 3: DateTime 24-hour with 15-minute Increments

```typescript
// datetime-24hr-increment.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-24hr-increment',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>DateTime (24-hour, 15-min increments)</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [dateFormat]="'Y-m-d H:i'"
        [time_24hr]="true"
        [minuteIncrement]="15"
        [defaultHour]="9"
        [defaultMinute]="0"
        placeholder="Choose date and time..."
        class="form-control"
      />
    </div>
  `
})
export class DateTime24HrIncrementComponent {
  selectedDateTime: Date | null = null;
}
```

---

## 5. DateTime Picker - 12 Hour Format

### Example 1: Basic DateTime (12-hour with AM/PM)

```typescript
// datetime-12hr.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-12hr',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Select Date and Time (12-hour AM/PM)</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [dateFormat]="'Y-m-d h:i K'"
        [time_24hr]="false"
        placeholder="2024-01-15 02:30 PM"
        class="form-control"
      />
      <p>Selected: {{ selectedDateTime }}</p>
    </div>
  `
})
export class DateTime12HrComponent {
  selectedDateTime: Date | null = null;
}
```

**Key Configuration:**
- `[time_24hr]="false"` - Enables 12-hour format with AM/PM
- `[dateFormat]="'Y-m-d h:i K'"` - `h` for 12-hour, `K` for AM/PM

### Example 2: DateTime 12-hour with Friendly Display

```typescript
// datetime-12hr-friendly.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-12hr-friendly',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>DateTime with Friendly Display</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [dateFormat]="'Y-m-d h:i K'"
        [time_24hr]="false"
        [altInput]="true"
        [altFormat]="'F j, Y at h:i K'"
        placeholder="January 15, 2024 at 2:30 PM"
        class="form-control"
      />
    </div>
  `
})
export class DateTime12HrFriendlyComponent {
  selectedDateTime: Date | null = null;
}
```

**Display:** Shows "January 15, 2024 at 2:30 PM" but stores "2024-01-15 02:30 PM"

### Example 3: DateTime 12-hour with Seconds

```typescript
// datetime-12hr-seconds.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-12hr-seconds',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>DateTime 12-hour with Seconds</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [enableSeconds]="true"
        [dateFormat]="'Y-m-d h:i:S K'"
        [time_24hr]="false"
        placeholder="2024-01-15 02:30:45 PM"
        class="form-control"
      />
    </div>
  `
})
export class DateTime12HrSecondsComponent {
  selectedDateTime: Date | null = null;
}
```

---

## 6. DateTime Picker with Timezone

> **Note:** Flatpickr doesn't have built-in timezone support. Use these patterns to handle timezones in your application.

### Example 1: DateTime with Timezone Display

```typescript
// datetime-timezone.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-timezone',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Select Date and Time</label>
      <div class="datetime-timezone-wrapper">
        <input
          type="text"
          mwlFlatpickr
          [(ngModel)]="selectedDateTime"
          [enableTime]="true"
          [dateFormat]="'Y-m-d H:i'"
          [time_24hr]="true"
          (flatpickrChange)="onDateTimeChange($event)"
          placeholder="Choose date and time..."
          class="form-control"
        />
        <select [(ngModel)]="selectedTimezone" (change)="onTimezoneChange()" class="timezone-select">
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
      <p class="timezone-display">
        <strong>Local:</strong> {{ selectedDateTime }}<br>
        <strong>UTC:</strong> {{ utcDateTime }}<br>
        <strong>Selected TZ:</strong> {{ displayDateTime }}
      </p>
    </div>
  `,
  styles: [`
    .datetime-timezone-wrapper { display: flex; gap: 10px; }
    .form-control { flex: 2; }
    .timezone-select { flex: 1; padding: 10px; border: 1px solid #ccc; border-radius: 4px; }
    .timezone-display { margin-top: 10px; padding: 10px; background: #f5f5f5; border-radius: 4px; font-family: monospace; }
  `]
})
export class DateTimeTimezoneComponent {
  selectedDateTime: Date | null = null;
  selectedTimezone = 'UTC';
  utcDateTime = '';
  displayDateTime = '';

  onDateTimeChange(event: any): void {
    if (event.selectedDates[0]) {
      const date = event.selectedDates[0];
      this.utcDateTime = date.toISOString();
      this.updateDisplayDateTime(date);
    }
  }

  onTimezoneChange(): void {
    if (this.selectedDateTime) {
      this.updateDisplayDateTime(new Date(this.selectedDateTime));
    }
  }

  updateDisplayDateTime(date: Date): void {
    this.displayDateTime = date.toLocaleString('en-US', {
      timeZone: this.selectedTimezone,
      year: 'numeric',
      month: '2-digit',
      day: '2-digit',
      hour: '2-digit',
      minute: '2-digit',
      hour12: false
    });
  }
}
```

### Example 2: DateTime with Timezone Offset Display

```typescript
// datetime-timezone-offset.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-timezone-offset',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>DateTime with Timezone Offset</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [dateFormat]="'Y-m-d H:i'"
        [time_24hr]="true"
        (flatpickrChange)="onDateTimeChange($event)"
        placeholder="Choose date and time..."
        class="form-control"
      />
      <div class="timezone-info">
        <p><strong>ISO 8601:</strong> {{ isoString }}</p>
        <p><strong>With Offset:</strong> {{ dateWithOffset }}</p>
        <p><strong>Timezone:</strong> {{ timezoneAbbr }} ({{ timezoneOffset }})</p>
      </div>
    </div>
  `,
  styles: [`
    .timezone-info { margin-top: 10px; padding: 15px; background: #e8f4fd; border-radius: 4px; font-family: monospace; font-size: 13px; }
    .timezone-info p { margin: 5px 0; }
  `]
})
export class DateTimeTimezoneOffsetComponent {
  selectedDateTime: Date | null = null;
  isoString = '';
  dateWithOffset = '';
  timezoneOffset = '';
  timezoneAbbr = '';

  onDateTimeChange(event: any): void {
    if (event.selectedDates[0]) {
      const date = event.selectedDates[0];
      this.isoString = date.toISOString();

      // Calculate timezone offset
      const offset = -date.getTimezoneOffset();
      const hours = Math.floor(Math.abs(offset) / 60);
      const minutes = Math.abs(offset) % 60;
      const sign = offset >= 0 ? '+' : '-';
      this.timezoneOffset = `${sign}${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}`;

      // Format with offset
      const year = date.getFullYear();
      const month = (date.getMonth() + 1).toString().padStart(2, '0');
      const day = date.getDate().toString().padStart(2, '0');
      const hour = date.getHours().toString().padStart(2, '0');
      const minute = date.getMinutes().toString().padStart(2, '0');
      this.dateWithOffset = `${year}-${month}-${day}T${hour}:${minute}:00${this.timezoneOffset}`;

      // Get timezone abbreviation
      this.timezoneAbbr = Intl.DateTimeFormat('en', { timeZoneName: 'short' })
        .formatToParts(date)
        .find(part => part.type === 'timeZoneName')?.value || '';
    }
  }
}
```

### Example 3: UTC DateTime Picker

```typescript
// datetime-utc.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-utc',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Select DateTime (Stored as UTC)</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="localDateTime"
        [enableTime]="true"
        [dateFormat]="'Y-m-d H:i'"
        [time_24hr]="true"
        (flatpickrChange)="convertToUTC($event)"
        placeholder="Choose date and time..."
        class="form-control"
      />
      <div class="utc-display">
        <p><strong>Local Time:</strong> {{ localDateTime }}</p>
        <p><strong>UTC Time:</strong> {{ utcDateTime }}</p>
        <p><strong>ISO String:</strong> {{ isoString }}</p>
      </div>
    </div>
  `,
  styles: [`
    .utc-display { margin-top: 10px; padding: 15px; background: #fff3cd; border-radius: 4px; font-family: monospace; }
  `]
})
export class DateTimeUtcComponent {
  localDateTime: Date | null = null;
  utcDateTime = '';
  isoString = '';

  convertToUTC(event: any): void {
    if (event.selectedDates[0]) {
      const localDate = event.selectedDates[0];

      // Convert to UTC string
      this.utcDateTime = localDate.toUTCString();

      // ISO 8601 format (always UTC with Z suffix)
      this.isoString = localDate.toISOString();
    }
  }
}
```

### Example 4: Multi-Timezone DateTime Comparison

```typescript
// datetime-multi-timezone.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-multi-timezone',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Select DateTime to see in Multiple Timezones</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [dateFormat]="'Y-m-d H:i'"
        [time_24hr]="true"
        (flatpickrChange)="updateAllTimezones($event)"
        placeholder="Choose date and time..."
        class="form-control"
      />

      @if (selectedDateTime) {
        <div class="timezone-grid">
          @for (tz of timezones; track tz.id) {
            <div class="timezone-card">
              <div class="tz-name">{{ tz.name }}</div>
              <div class="tz-time">{{ tz.time }}</div>
              <div class="tz-offset">{{ tz.id }}</div>
            </div>
          }
        </div>
      }
    </div>
  `,
  styles: [`
    .timezone-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); gap: 10px; margin-top: 15px; }
    .timezone-card { padding: 15px; background: #f8f9fa; border-radius: 8px; border-left: 4px solid #007bff; }
    .tz-name { font-weight: bold; color: #333; }
    .tz-time { font-size: 18px; font-family: monospace; margin: 5px 0; }
    .tz-offset { font-size: 12px; color: #666; }
  `]
})
export class DateTimeMultiTimezoneComponent {
  selectedDateTime: Date | null = null;

  timezones = [
    { id: 'UTC', name: 'UTC', time: '' },
    { id: 'America/New_York', name: 'New York (ET)', time: '' },
    { id: 'America/Los_Angeles', name: 'Los Angeles (PT)', time: '' },
    { id: 'Europe/London', name: 'London (GMT)', time: '' },
    { id: 'Europe/Paris', name: 'Paris (CET)', time: '' },
    { id: 'Asia/Tokyo', name: 'Tokyo (JST)', time: '' },
    { id: 'Asia/Kolkata', name: 'Mumbai (IST)', time: '' },
    { id: 'Australia/Sydney', name: 'Sydney (AEDT)', time: '' }
  ];

  updateAllTimezones(event: any): void {
    if (event.selectedDates[0]) {
      const date = event.selectedDates[0];

      this.timezones = this.timezones.map(tz => ({
        ...tz,
        time: date.toLocaleString('en-US', {
          timeZone: tz.id,
          year: 'numeric',
          month: 'short',
          day: '2-digit',
          hour: '2-digit',
          minute: '2-digit',
          hour12: false
        })
      }));
    }
  }
}
```

---

## 7. Time Only Picker

### Example 1: Basic Time Picker

```typescript
// time-picker.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-time-picker',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Select Time</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedTime"
        [enableTime]="true"
        [noCalendar]="true"
        [dateFormat]="'H:i'"
        [time_24hr]="true"
        placeholder="Choose time..."
        class="form-control"
      />
      <p>Selected: {{ selectedTime }}</p>
    </div>
  `
})
export class TimePickerComponent {
  selectedTime: string = '';
}
```

### Example 2: Time Picker with Min/Max

```typescript
// time-limits.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-time-limits',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Business Hours Only (9 AM - 5 PM)</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedTime"
        [enableTime]="true"
        [noCalendar]="true"
        [dateFormat]="'H:i'"
        [time_24hr]="true"
        [minTime]="'09:00'"
        [maxTime]="'17:00'"
        [minuteIncrement]="30"
        placeholder="Choose time..."
        class="form-control"
      />
    </div>
  `
})
export class TimeLimitsComponent {
  selectedTime: string = '';
}
```

---

## 8. Date Range Picker

### Example 1: Basic Date Range

```typescript
// date-range.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-date-range',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Select Date Range</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="dateRange"
        [mode]="'range'"
        [dateFormat]="'Y-m-d'"
        placeholder="Choose date range..."
        class="form-control"
      />
      <p>Selected Range: {{ dateRange }}</p>
    </div>
  `
})
export class DateRangeComponent {
  dateRange: string = '';
}
```

### Example 2: DateTime Range Picker

```typescript
// datetime-range.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-range',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Select DateTime Range</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="dateTimeRange"
        [mode]="'range'"
        [enableTime]="true"
        [dateFormat]="'Y-m-d H:i'"
        [time_24hr]="true"
        placeholder="Choose datetime range..."
        class="form-control"
      />
    </div>
  `
})
export class DateTimeRangeComponent {
  dateTimeRange: string = '';
}
```

### Example 3: Two Separate Inputs for Range

```typescript
// separate-range.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-separate-range',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-row">
      <div class="form-group">
        <label>Start Date</label>
        <input
          type="text"
          mwlFlatpickr
          [(ngModel)]="startDate"
          [enableTime]="true"
          [dateFormat]="'Y-m-d H:i'"
          [maxDate]="endDate"
          (flatpickrChange)="onStartDateChange($event)"
          placeholder="Start date..."
          class="form-control"
        />
      </div>
      <div class="form-group">
        <label>End Date</label>
        <input
          type="text"
          mwlFlatpickr
          [(ngModel)]="endDate"
          [enableTime]="true"
          [dateFormat]="'Y-m-d H:i'"
          [minDate]="startDate"
          (flatpickrChange)="onEndDateChange($event)"
          placeholder="End date..."
          class="form-control"
        />
      </div>
    </div>
  `
})
export class SeparateRangeComponent {
  startDate: Date | null = null;
  endDate: Date | null = null;

  onStartDateChange(event: any): void {
    console.log('Start date changed:', event.dateString);
  }

  onEndDateChange(event: any): void {
    console.log('End date changed:', event.dateString);
  }
}
```

---

## 11. Advanced Configuration

### Example 1: With Events/Callbacks

```typescript
// datetime-events.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-datetime-events',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>DateTime with Events</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [dateFormat]="'Y-m-d H:i'"
        (flatpickrChange)="onChange($event)"
        (flatpickrOpen)="onOpen()"
        (flatpickrClose)="onClose()"
        (flatpickrReady)="onReady($event)"
        placeholder="Choose datetime..."
        class="form-control"
      />
      <div class="event-log">
        @for (log of eventLogs; track log) {
          <p>{{ log }}</p>
        }
      </div>
    </div>
  `
})
export class DateTimeEventsComponent {
  selectedDateTime: Date | null = null;
  eventLogs: string[] = [];

  onChange(event: any): void {
    this.eventLogs.push(`Changed: ${event.dateString}`);
  }

  onOpen(): void {
    this.eventLogs.push('Picker opened');
  }

  onClose(): void {
    this.eventLogs.push('Picker closed');
  }

  onReady(event: any): void {
    this.eventLogs.push('Picker ready');
  }
}
```

### Example 2: Inline Calendar with DateTime

```typescript
// inline-datetime.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-inline-datetime',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>Inline DateTime Picker</label>
      <div
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [inline]="true"
        [dateFormat]="'Y-m-d H:i'"
        [time_24hr]="true"
      ></div>
      <p>Selected: {{ selectedDateTime }}</p>
    </div>
  `
})
export class InlineDateTimeComponent {
  selectedDateTime: Date | null = null;
}
```

### Example 3: Disable Specific Dates/Times

```typescript
// disabled-dates.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-disabled-dates',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>DateTime (Weekends disabled)</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [dateFormat]="'Y-m-d H:i'"
        [disable]="disabledDates"
        placeholder="Choose datetime..."
        class="form-control"
      />
    </div>
  `
})
export class DisabledDatesComponent {
  selectedDateTime: Date | null = null;

  // Disable weekends
  disabledDates = [
    (date: Date) => date.getDay() === 0 || date.getDay() === 6
  ];
}
```

### Example 4: With Locale

```typescript
// localized-datetime.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';
import { German } from 'flatpickr/dist/l10n/de';

@Component({
  selector: 'app-localized-datetime',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <div class="form-group">
      <label>DateTime (German Locale)</label>
      <input
        type="text"
        mwlFlatpickr
        [(ngModel)]="selectedDateTime"
        [enableTime]="true"
        [dateFormat]="'d.m.Y H:i'"
        [locale]="germanLocale"
        placeholder="Datum und Zeit wählen..."
        class="form-control"
      />
    </div>
  `
})
export class LocalizedDateTimeComponent {
  selectedDateTime: Date | null = null;
  germanLocale = German;
}
```

---

## 9. Reactive Forms Integration

### Complete Reactive Form Example

```typescript
// reactive-form.component.ts
import { Component, OnInit, inject } from '@angular/core';
import { FormBuilder, FormGroup, ReactiveFormsModule, Validators } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';
import { JsonPipe } from '@angular/common';

@Component({
  selector: 'app-reactive-form',
  imports: [ReactiveFormsModule, FlatpickrModule, JsonPipe],
  template: `
    <form [formGroup]="bookingForm" (ngSubmit)="onSubmit()">
      <div class="form-group">
        <label>Event Name</label>
        <input type="text" formControlName="eventName" class="form-control" />
      </div>

      <div class="form-group">
        <label>Event Date</label>
        <input
          type="text"
          mwlFlatpickr
          formControlName="eventDate"
          [dateFormat]="'Y-m-d'"
          [minDate]="today"
          placeholder="Select event date..."
          class="form-control"
        />
        @if (bookingForm.get('eventDate')?.errors?.['required']) {
          <div class="error">Event date is required</div>
        }
      </div>

      <div class="form-group">
        <label>Start Time</label>
        <input
          type="text"
          mwlFlatpickr
          formControlName="startTime"
          [enableTime]="true"
          [noCalendar]="true"
          [dateFormat]="'H:i'"
          [time_24hr]="true"
          [minuteIncrement]="15"
          placeholder="Select start time..."
          class="form-control"
        />
      </div>

      <div class="form-group">
        <label>End Time</label>
        <input
          type="text"
          mwlFlatpickr
          formControlName="endTime"
          [enableTime]="true"
          [noCalendar]="true"
          [dateFormat]="'H:i'"
          [time_24hr]="true"
          [minuteIncrement]="15"
          placeholder="Select end time..."
          class="form-control"
        />
      </div>

      <div class="form-group">
        <label>Full DateTime</label>
        <input
          type="text"
          mwlFlatpickr
          formControlName="fullDateTime"
          [enableTime]="true"
          [dateFormat]="'Y-m-d H:i'"
          [time_24hr]="true"
          [minuteIncrement]="5"
          placeholder="Select full datetime..."
          class="form-control"
        />
      </div>

      <button type="submit" [disabled]="bookingForm.invalid">Submit</button>
    </form>

    @if (bookingForm.valid) {
      <div class="form-values">
        <h3>Form Values:</h3>
        <pre>{{ bookingForm.value | json }}</pre>
      </div>
    }
  `,
  styles: [`
    .form-group { margin-bottom: 15px; }
    .form-control { width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 4px; }
    .error { color: red; font-size: 12px; margin-top: 5px; }
    button { padding: 10px 20px; background: #007bff; color: white; border: none; border-radius: 4px; cursor: pointer; }
    button:disabled { background: #ccc; }
  `]
})
export class ReactiveFormComponent implements OnInit {
  private fb = inject(FormBuilder);

  bookingForm!: FormGroup;
  today = new Date();

  ngOnInit(): void {
    this.bookingForm = this.fb.group({
      eventName: ['', Validators.required],
      eventDate: [null, Validators.required],
      startTime: ['09:00'],
      endTime: ['17:00'],
      fullDateTime: [null]
    });
  }

  onSubmit(): void {
    if (this.bookingForm.valid) {
      console.log('Form submitted:', this.bookingForm.value);
    }
  }
}
```

---

## 10. Template-Driven Forms

### Complete Template-Driven Form Example

```typescript
// template-form.component.ts
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { FlatpickrModule } from 'angularx-flatpickr';

@Component({
  selector: 'app-template-form',
  imports: [FormsModule, FlatpickrModule],
  template: `
    <form #appointmentForm="ngForm" (ngSubmit)="onSubmit(appointmentForm)">
      <div class="form-group">
        <label>Patient Name</label>
        <input
          type="text"
          name="patientName"
          [(ngModel)]="appointment.patientName"
          required
          class="form-control"
        />
      </div>

      <div class="form-group">
        <label>Appointment Date</label>
        <input
          type="text"
          name="appointmentDate"
          mwlFlatpickr
          [(ngModel)]="appointment.date"
          [dateFormat]="'Y-m-d'"
          [minDate]="today"
          required
          #dateField="ngModel"
          placeholder="Select date..."
          class="form-control"
        />
        @if (dateField.invalid && dateField.touched) {
          <div class="error">Date is required</div>
        }
      </div>

      <div class="form-group">
        <label>Appointment Time</label>
        <input
          type="text"
          name="appointmentTime"
          mwlFlatpickr
          [(ngModel)]="appointment.time"
          [enableTime]="true"
          [noCalendar]="true"
          [dateFormat]="'H:i'"
          [time_24hr]="true"
          [minTime]="'08:00'"
          [maxTime]="'18:00'"
          [minuteIncrement]="30"
          required
          placeholder="Select time..."
          class="form-control"
        />
      </div>

      <div class="form-group">
        <label>Full DateTime (Alternative)</label>
        <input
          type="text"
          name="fullDateTime"
          mwlFlatpickr
          [(ngModel)]="appointment.fullDateTime"
          [enableTime]="true"
          [dateFormat]="'Y-m-d H:i'"
          [time_24hr]="true"
          [minuteIncrement]="15"
          placeholder="Select datetime..."
          class="form-control"
        />
      </div>

      <button type="submit" [disabled]="appointmentForm.invalid">
        Book Appointment
      </button>
    </form>

    @if (submitted) {
      <div class="result">
        <h3>Appointment Details:</h3>
        <p><strong>Name:</strong> {{ appointment.patientName }}</p>
        <p><strong>Date:</strong> {{ appointment.date }}</p>
        <p><strong>Time:</strong> {{ appointment.time }}</p>
        <p><strong>Full DateTime:</strong> {{ appointment.fullDateTime }}</p>
      </div>
    }
  `
})
export class TemplateFormComponent {
  today = new Date();
  submitted = false;

  appointment = {
    patientName: '',
    date: null as Date | null,
    time: '',
    fullDateTime: null as Date | null
  };

  onSubmit(form: any): void {
    if (form.valid) {
      this.submitted = true;
      console.log('Appointment booked:', this.appointment);
    }
  }
}
```

---

## 10. Custom Styling

### Custom Theme Example

```css
/* styles.css or component styles */

/* Custom Flatpickr Theme */
.flatpickr-calendar {
  border-radius: 8px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
  border: none;
}

.flatpickr-calendar.arrowTop::before,
.flatpickr-calendar.arrowTop::after {
  border-bottom-color: #007bff;
}

.flatpickr-months {
  background: #007bff;
  border-radius: 8px 8px 0 0;
}

.flatpickr-months .flatpickr-month {
  color: white;
}

.flatpickr-current-month .flatpickr-monthDropdown-months,
.flatpickr-current-month input.cur-year {
  color: white;
}

.flatpickr-weekdays {
  background: #007bff;
}

.flatpickr-weekday {
  color: rgba(255, 255, 255, 0.8);
}

.flatpickr-day.selected,
.flatpickr-day.selected:hover {
  background: #007bff;
  border-color: #007bff;
}

.flatpickr-day:hover {
  background: #e6f0ff;
}

.flatpickr-time {
  border-top: 1px solid #eee;
}

.flatpickr-time input {
  font-size: 16px;
}

/* Custom input styling */
.datetime-input {
  width: 100%;
  padding: 12px 16px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 14px;
  transition: all 0.3s ease;
}

.datetime-input:focus {
  border-color: #007bff;
  box-shadow: 0 0 0 3px rgba(0, 123, 255, 0.1);
  outline: none;
}

.datetime-input::placeholder {
  color: #aaa;
}
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
