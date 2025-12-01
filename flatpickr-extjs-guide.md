# Flatpickr DateTime Picker for ExtJS

A comprehensive step-by-step guide for integrating Flatpickr date/time picker in ExtJS applications as an alternative to native date/time fields.

---

## Table of Contents

1. [Installation](#1-installation)
2. [Creating Custom ExtJS Components](#2-creating-custom-extjs-components)
3. [Date Picker (Date Only)](#3-date-picker-date-only)
4. [DateTime Picker - 24 Hour Format](#4-datetime-picker---24-hour-format)
5. [DateTime Picker - 12 Hour Format](#5-datetime-picker---12-hour-format)
6. [DateTime Picker with Timezone](#6-datetime-picker-with-timezone)
7. [Time Only Picker](#7-time-only-picker)
8. [Date Range Picker](#8-date-range-picker)
9. [Form Panel Integration](#9-form-panel-integration)
10. [Complete Working Example](#10-complete-working-example)

---

## 1. Installation

### Step 1: Include Flatpickr

**Using CDN:**

```html
<!-- Flatpickr CSS -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/themes/airbnb.css">

<!-- Flatpickr JS -->
<script src="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.js"></script>
```

**Using npm (for Sencha CMD projects):**

```bash
npm install flatpickr
```

Then in your app.json:

```json
{
    "css": [
        {
            "path": "node_modules/flatpickr/dist/flatpickr.min.css"
        }
    ],
    "js": [
        {
            "path": "node_modules/flatpickr/dist/flatpickr.min.js",
            "bundle": true
        }
    ]
}
```

### Step 2: Ensure ExtJS is loaded

```html
<!-- ExtJS (requires license for modern versions) -->
<script src="path/to/ext-all.js"></script>
<link rel="stylesheet" href="path/to/ext-theme.css">
```

---

## 2. Creating Custom ExtJS Components

### FlatpickrField - Base Component

```javascript
/**
 * Ext.ux.FlatpickrField
 * Custom ExtJS form field that uses Flatpickr
 */
Ext.define('Ext.ux.FlatpickrField', {
    extend: 'Ext.form.field.Text',
    alias: 'widget.flatpickrfield',

    config: {
        /**
         * @cfg {Object} flatpickrConfig
         * Flatpickr configuration options
         */
        flatpickrConfig: null,

        /**
         * @cfg {Boolean} enableTime
         * Enable time picker
         */
        enableTime: false,

        /**
         * @cfg {Boolean} noCalendar
         * Time picker only (no calendar)
         */
        noCalendar: false,

        /**
         * @cfg {String} dateFormat
         * Date format string
         */
        dateFormat: 'Y-m-d',

        /**
         * @cfg {Boolean} time_24hr
         * Use 24-hour time format
         */
        time_24hr: true,

        /**
         * @cfg {Number} minuteIncrement
         * Minute increment for time picker
         */
        minuteIncrement: 5,

        /**
         * @cfg {Date/String} minDate
         * Minimum selectable date
         */
        minDate: null,

        /**
         * @cfg {Date/String} maxDate
         * Maximum selectable date
         */
        maxDate: null
    },

    flatpickrInstance: null,

    initComponent: function() {
        this.callParent(arguments);
        this.on('afterrender', this.initFlatpickr, this);
        this.on('destroy', this.destroyFlatpickr, this);
    },

    initFlatpickr: function() {
        var me = this;
        var inputEl = me.inputEl.dom;

        // Build configuration
        var config = Ext.apply({
            enableTime: me.getEnableTime(),
            noCalendar: me.getNoCalendar(),
            dateFormat: me.getDateFormat(),
            time_24hr: me.getTime_24hr(),
            minuteIncrement: me.getMinuteIncrement(),
            minDate: me.getMinDate(),
            maxDate: me.getMaxDate(),
            onChange: function(selectedDates, dateStr, instance) {
                me.setValue(dateStr);
                me.fireEvent('change', me, dateStr, selectedDates);
            },
            onOpen: function(selectedDates, dateStr, instance) {
                me.fireEvent('open', me, dateStr, selectedDates);
            },
            onClose: function(selectedDates, dateStr, instance) {
                me.fireEvent('close', me, dateStr, selectedDates);
            }
        }, me.getFlatpickrConfig());

        me.flatpickrInstance = flatpickr(inputEl, config);
    },

    destroyFlatpickr: function() {
        if (this.flatpickrInstance) {
            this.flatpickrInstance.destroy();
            this.flatpickrInstance = null;
        }
    },

    // Override setValue to sync with Flatpickr
    setValue: function(value) {
        this.callParent(arguments);
        if (this.flatpickrInstance && value) {
            this.flatpickrInstance.setDate(value, false);
        }
        return this;
    },

    // Set min date dynamically
    setMinDate: function(date) {
        if (this.flatpickrInstance) {
            this.flatpickrInstance.set('minDate', date);
        }
    },

    // Set max date dynamically
    setMaxDate: function(date) {
        if (this.flatpickrInstance) {
            this.flatpickrInstance.set('maxDate', date);
        }
    },

    // Clear the picker
    clear: function() {
        if (this.flatpickrInstance) {
            this.flatpickrInstance.clear();
        }
        this.setValue('');
    },

    // Open the picker programmatically
    open: function() {
        if (this.flatpickrInstance) {
            this.flatpickrInstance.open();
        }
    },

    // Close the picker programmatically
    close: function() {
        if (this.flatpickrInstance) {
            this.flatpickrInstance.close();
        }
    }
});
```

### FlatpickrDateField - Date Only

```javascript
/**
 * Ext.ux.FlatpickrDateField
 * Date picker using Flatpickr
 */
Ext.define('Ext.ux.FlatpickrDateField', {
    extend: 'Ext.ux.FlatpickrField',
    alias: 'widget.flatpickrdatefield',

    config: {
        enableTime: false,
        noCalendar: false,
        dateFormat: 'Y-m-d'
    }
});
```

### FlatpickrTimeField - Time Only

```javascript
/**
 * Ext.ux.FlatpickrTimeField
 * Time picker using Flatpickr
 */
Ext.define('Ext.ux.FlatpickrTimeField', {
    extend: 'Ext.ux.FlatpickrField',
    alias: 'widget.flatpickrtimefield',

    config: {
        enableTime: true,
        noCalendar: true,
        dateFormat: 'H:i',
        time_24hr: true,
        minuteIncrement: 15
    }
});
```

### FlatpickrDateTimeField - Date and Time

```javascript
/**
 * Ext.ux.FlatpickrDateTimeField
 * DateTime picker using Flatpickr
 */
Ext.define('Ext.ux.FlatpickrDateTimeField', {
    extend: 'Ext.ux.FlatpickrField',
    alias: 'widget.flatpickrdatetimefield',

    config: {
        enableTime: true,
        noCalendar: false,
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        minuteIncrement: 5
    }
});
```

---

## 3. Date Picker (Date Only)

### Example 1: Simple Date Picker (No Time)

```javascript
Ext.create('Ext.form.Panel', {
    title: 'Date Picker Example',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatefield',
        fieldLabel: 'Select Date',
        name: 'date',
        emptyText: 'Choose a date...',
        dateFormat: 'Y-m-d',
        listeners: {
            change: function(field, newValue) {
                console.log('Date selected:', newValue);
            }
        }
    }]
});
```

### Example 2: Date Picker with Min/Max

```javascript
var today = new Date();
var maxDate = new Date();
maxDate.setDate(maxDate.getDate() + 30);

Ext.create('Ext.form.Panel', {
    title: 'Date Range Limits',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatefield',
        fieldLabel: 'Date (Next 30 days)',
        name: 'date',
        emptyText: 'Choose a date...',
        minDate: today,
        maxDate: maxDate
    }]
});
```

### Example 3: Multiple Dates Selection

```javascript
Ext.create('Ext.form.Panel', {
    title: 'Multiple Dates',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrfield',
        fieldLabel: 'Select Dates',
        name: 'dates',
        emptyText: 'Choose multiple dates...',
        flatpickrConfig: {
            mode: 'multiple',
            dateFormat: 'Y-m-d',
            conjunction: ', '
        }
    }]
});
```

---

## 4. DateTime Picker - 24 Hour Format

### Example 1: Basic 24-Hour DateTime

```javascript
Ext.create('Ext.form.Panel', {
    title: 'DateTime Picker (24-Hour)',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Date & Time',
        name: 'datetime',
        emptyText: 'Select date and time...',
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        listeners: {
            change: function(field, newValue) {
                console.log('DateTime selected:', newValue);
            }
        }
    }]
});
```

### Example 2: 24-Hour with Default Time

```javascript
Ext.create('Ext.form.Panel', {
    title: 'DateTime with Default (24-Hour)',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Meeting Time',
        name: 'datetime',
        emptyText: 'Select datetime...',
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        flatpickrConfig: {
            defaultHour: 14,
            defaultMinute: 30
        }
    }]
});
```

### Example 3: 24-Hour with Seconds

```javascript
Ext.create('Ext.form.Panel', {
    title: 'DateTime with Seconds (24-Hour)',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrfield',
        fieldLabel: 'Precise DateTime',
        name: 'datetime',
        emptyText: 'Select datetime...',
        enableTime: true,
        dateFormat: 'Y-m-d H:i:S',
        time_24hr: true,
        flatpickrConfig: {
            enableSeconds: true
        }
    }]
});
```

### Example 4: 24-Hour with 15-Minute Increments

```javascript
Ext.create('Ext.form.Panel', {
    title: 'DateTime (15-min increments)',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Appointment',
        name: 'datetime',
        emptyText: 'Select datetime...',
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        minuteIncrement: 15
    }]
});
```

---

## 5. DateTime Picker - 12 Hour Format

### Example 1: Basic 12-Hour DateTime

```javascript
Ext.create('Ext.form.Panel', {
    title: 'DateTime Picker (12-Hour)',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Date & Time',
        name: 'datetime',
        emptyText: 'Select date and time...',
        dateFormat: 'Y-m-d h:i K',
        time_24hr: false,
        listeners: {
            change: function(field, newValue) {
                console.log('DateTime selected:', newValue);
            }
        }
    }]
});
```

### Example 2: 12-Hour with Default AM/PM

```javascript
Ext.create('Ext.form.Panel', {
    title: 'DateTime with Default (12-Hour)',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Morning Meeting',
        name: 'datetime',
        emptyText: 'Select datetime...',
        dateFormat: 'Y-m-d h:i K',
        time_24hr: false,
        flatpickrConfig: {
            defaultHour: 9,
            defaultMinute: 0
        }
    }]
});
```

### Example 3: 12-Hour with Alternate Display Format

```javascript
Ext.create('Ext.form.Panel', {
    title: 'DateTime (Friendly Format)',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Event Time',
        name: 'datetime',
        emptyText: 'Select datetime...',
        dateFormat: 'F j, Y h:i K',  // December 1, 2025 02:30 PM
        time_24hr: false
    }]
});
```

### Example 4: 12-Hour with 30-Minute Increments

```javascript
Ext.create('Ext.form.Panel', {
    title: 'DateTime (30-min increments)',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Consultation',
        name: 'datetime',
        emptyText: 'Select datetime...',
        dateFormat: 'Y-m-d h:i K',
        time_24hr: false,
        minuteIncrement: 30
    }]
});
```

---

## 6. DateTime Picker with Timezone

> **Note:** Flatpickr doesn't have built-in timezone support. These examples show how to implement timezone handling in ExtJS.

### Example 1: DateTime with Timezone Selector

```javascript
// Timezone utility singleton
Ext.define('App.util.Timezone', {
    singleton: true,

    timezones: [
        { abbr: 'UTC', name: 'UTC', offset: 0 },
        { abbr: 'EST', name: 'America/New_York', offset: -5 },
        { abbr: 'CST', name: 'America/Chicago', offset: -6 },
        { abbr: 'MST', name: 'America/Denver', offset: -7 },
        { abbr: 'PST', name: 'America/Los_Angeles', offset: -8 },
        { abbr: 'GMT', name: 'Europe/London', offset: 0 },
        { abbr: 'CET', name: 'Europe/Paris', offset: 1 },
        { abbr: 'IST', name: 'Asia/Kolkata', offset: 5.5 },
        { abbr: 'JST', name: 'Asia/Tokyo', offset: 9 },
        { abbr: 'AEST', name: 'Australia/Sydney', offset: 11 }
    ],

    convertToTimezone: function(date, timezone) {
        if (!date) return null;
        return new Date(date.toLocaleString('en-US', { timeZone: timezone }));
    },

    formatInTimezone: function(date, timezone, format24hr) {
        if (!date) return '';
        var options = {
            timeZone: timezone,
            year: 'numeric',
            month: '2-digit',
            day: '2-digit',
            hour: '2-digit',
            minute: '2-digit',
            hour12: !format24hr
        };
        return new Intl.DateTimeFormat('en-US', options).format(date);
    }
});

// DateTime with Timezone Panel
Ext.create('Ext.form.Panel', {
    title: 'DateTime with Timezone',
    renderTo: Ext.getBody(),
    width: 500,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Select DateTime',
        name: 'datetime',
        itemId: 'datetimeField',
        emptyText: 'Select date and time...',
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        listeners: {
            change: function(field, newValue) {
                var form = field.up('form');
                form.updateTimezoneDisplay();
            }
        }
    }, {
        xtype: 'combobox',
        fieldLabel: 'Timezone',
        name: 'timezone',
        itemId: 'timezoneCombo',
        store: Ext.create('Ext.data.Store', {
            fields: ['abbr', 'name', 'offset'],
            data: App.util.Timezone.timezones
        }),
        displayField: 'abbr',
        valueField: 'name',
        value: 'America/New_York',
        editable: false,
        listeners: {
            change: function(combo, newValue) {
                combo.up('form').updateTimezoneDisplay();
            }
        }
    }, {
        xtype: 'displayfield',
        fieldLabel: 'Local Time',
        itemId: 'localTimeDisplay'
    }, {
        xtype: 'displayfield',
        fieldLabel: 'Selected TZ Time',
        itemId: 'tzTimeDisplay'
    }],

    updateTimezoneDisplay: function() {
        var datetime = this.down('#datetimeField').getValue();
        var timezone = this.down('#timezoneCombo').getValue();

        if (datetime) {
            var date = new Date(datetime);
            this.down('#localTimeDisplay').setValue(
                date.toLocaleString('en-US', { hour12: false })
            );
            this.down('#tzTimeDisplay').setValue(
                App.util.Timezone.formatInTimezone(date, timezone, true)
            );
        }
    }
});
```

### Example 2: DateTime with Timezone Offset Display

```javascript
Ext.create('Ext.form.Panel', {
    title: 'DateTime with Offset Display',
    renderTo: Ext.getBody(),
    width: 500,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Select DateTime',
        name: 'datetime',
        itemId: 'datetimeField',
        emptyText: 'Select date and time...',
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        listeners: {
            change: function(field, newValue) {
                if (newValue) {
                    var date = new Date(newValue);
                    var offset = -date.getTimezoneOffset();
                    var offsetHours = Math.floor(Math.abs(offset) / 60);
                    var offsetMins = Math.abs(offset) % 60;
                    var sign = offset >= 0 ? '+' : '-';
                    var offsetStr = sign +
                        String(offsetHours).padStart(2, '0') + ':' +
                        String(offsetMins).padStart(2, '0');

                    var form = field.up('form');
                    form.down('#offsetDisplay').setValue('UTC' + offsetStr);
                    form.down('#isoDisplay').setValue(date.toISOString());
                }
            }
        }
    }, {
        xtype: 'displayfield',
        fieldLabel: 'Timezone Offset',
        itemId: 'offsetDisplay',
        value: 'Not selected'
    }, {
        xtype: 'displayfield',
        fieldLabel: 'ISO Format (UTC)',
        itemId: 'isoDisplay',
        value: 'Not selected'
    }]
});
```

### Example 3: UTC DateTime Picker

```javascript
Ext.create('Ext.form.Panel', {
    title: 'UTC DateTime Picker',
    renderTo: Ext.getBody(),
    width: 500,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Select Local Time',
        name: 'localDatetime',
        itemId: 'localField',
        emptyText: 'Select date and time...',
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        listeners: {
            change: function(field, newValue) {
                if (newValue) {
                    var localDate = new Date(newValue);
                    var utcString = localDate.toISOString().slice(0, 16).replace('T', ' ');
                    field.up('form').down('#utcDisplay').setValue(utcString + ' UTC');
                }
            }
        }
    }, {
        xtype: 'displayfield',
        fieldLabel: 'UTC Equivalent',
        itemId: 'utcDisplay',
        value: 'Not selected'
    }],

    // Method to get UTC value
    getUTCValue: function() {
        var localValue = this.down('#localField').getValue();
        if (localValue) {
            return new Date(localValue).toISOString();
        }
        return null;
    }
});
```

### Example 4: Multi-Timezone Comparison

```javascript
Ext.create('Ext.form.Panel', {
    title: 'Multi-Timezone Comparison',
    renderTo: Ext.getBody(),
    width: 600,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Base DateTime',
        name: 'baseDateTime',
        itemId: 'baseField',
        emptyText: 'Select date and time...',
        dateFormat: 'Y-m-d H:i',
        time_24hr: true,
        anchor: '100%',
        listeners: {
            change: function(field, newValue) {
                field.up('form').updateAllTimezones();
            }
        }
    }, {
        xtype: 'combobox',
        fieldLabel: 'Base Timezone',
        name: 'baseTimezone',
        itemId: 'baseTzCombo',
        store: Ext.create('Ext.data.Store', {
            fields: ['abbr', 'name'],
            data: App.util.Timezone.timezones
        }),
        displayField: 'abbr',
        valueField: 'name',
        value: 'America/New_York',
        editable: false,
        anchor: '100%',
        listeners: {
            change: function(combo) {
                combo.up('form').updateAllTimezones();
            }
        }
    }, {
        xtype: 'fieldset',
        title: 'Time in Different Zones',
        itemId: 'tzFieldset',
        defaults: {
            xtype: 'displayfield',
            anchor: '100%'
        },
        items: [
            { fieldLabel: 'New York (EST)', itemId: 'tzEST' },
            { fieldLabel: 'London (GMT)', itemId: 'tzGMT' },
            { fieldLabel: 'Tokyo (JST)', itemId: 'tzJST' },
            { fieldLabel: 'Sydney (AEST)', itemId: 'tzAEST' }
        ]
    }],

    updateAllTimezones: function() {
        var datetime = this.down('#baseField').getValue();
        var baseTz = this.down('#baseTzCombo').getValue();

        if (!datetime) return;

        var date = new Date(datetime);
        var formatOpts = {
            year: 'numeric',
            month: '2-digit',
            day: '2-digit',
            hour: '2-digit',
            minute: '2-digit',
            hour12: false
        };

        var timezones = {
            'tzEST': 'America/New_York',
            'tzGMT': 'Europe/London',
            'tzJST': 'Asia/Tokyo',
            'tzAEST': 'Australia/Sydney'
        };

        var me = this;
        Ext.Object.each(timezones, function(itemId, tz) {
            var formatted = new Intl.DateTimeFormat('en-US',
                Ext.apply({ timeZone: tz }, formatOpts)
            ).format(date);
            me.down('#' + itemId).setValue(formatted);
        });
    }
});
```

---

## 7. Time Only Picker

### Example 1: Basic Time Picker

```javascript
Ext.create('Ext.form.Panel', {
    title: 'Time Picker',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrtimefield',
        fieldLabel: 'Select Time',
        name: 'time',
        emptyText: 'Choose time...',
        listeners: {
            change: function(field, newValue) {
                console.log('Time selected:', newValue);
            }
        }
    }]
});
```

### Example 2: Time Picker with Min/Max

```javascript
Ext.create('Ext.form.Panel', {
    title: 'Business Hours',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrtimefield',
        fieldLabel: 'Time (9AM-5PM)',
        name: 'time',
        emptyText: 'Choose time...',
        minuteIncrement: 30,
        flatpickrConfig: {
            minTime: '09:00',
            maxTime: '17:00'
        }
    }]
});
```

### Example 3: Time Picker 12-hour Format

```javascript
Ext.create('Ext.form.Panel', {
    title: 'Time (12-hour)',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrtimefield',
        fieldLabel: 'Select Time',
        name: 'time',
        emptyText: 'Choose time...',
        dateFormat: 'h:i K',
        time_24hr: false
    }]
});
```

---

## 8. Date Range Picker

### Example 1: Basic Date Range

```javascript
Ext.create('Ext.form.Panel', {
    title: 'Date Range',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrfield',
        fieldLabel: 'Select Range',
        name: 'dateRange',
        emptyText: 'Choose date range...',
        flatpickrConfig: {
            mode: 'range',
            dateFormat: 'Y-m-d'
        },
        listeners: {
            change: function(field, newValue) {
                console.log('Range selected:', newValue);
            }
        }
    }]
});
```

### Example 2: DateTime Range

```javascript
Ext.create('Ext.form.Panel', {
    title: 'DateTime Range',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrfield',
        fieldLabel: 'DateTime Range',
        name: 'dateTimeRange',
        emptyText: 'Choose datetime range...',
        enableTime: true,
        time_24hr: true,
        flatpickrConfig: {
            mode: 'range',
            dateFormat: 'Y-m-d H:i'
        }
    }]
});
```

### Example 3: Linked Start/End Fields

```javascript
Ext.create('Ext.form.Panel', {
    title: 'Start/End Date',
    renderTo: Ext.getBody(),
    width: 500,
    bodyPadding: 10,
    layout: {
        type: 'hbox',
        align: 'stretch'
    },
    defaults: {
        flex: 1,
        margin: '0 5 0 0'
    },
    items: [{
        xtype: 'flatpickrdatetimefield',
        itemId: 'startDate',
        fieldLabel: 'Start',
        name: 'startDate',
        emptyText: 'Start date...',
        listeners: {
            change: function(field, newValue) {
                var form = field.up('form');
                var endField = form.down('#endDate');
                if (endField) {
                    endField.setMinDate(newValue);
                }
            }
        }
    }, {
        xtype: 'flatpickrdatetimefield',
        itemId: 'endDate',
        fieldLabel: 'End',
        name: 'endDate',
        emptyText: 'End date...',
        margin: 0,
        listeners: {
            change: function(field, newValue) {
                var form = field.up('form');
                var startField = form.down('#startDate');
                if (startField) {
                    startField.setMaxDate(newValue);
                }
            }
        }
    }]
});
```

---

## 9. Form Panel Integration

### Complete Booking Form Example

```javascript
Ext.create('Ext.form.Panel', {
    title: 'Event Booking',
    renderTo: Ext.getBody(),
    width: 500,
    bodyPadding: 15,
    defaults: {
        anchor: '100%',
        labelWidth: 120
    },
    items: [{
        xtype: 'textfield',
        fieldLabel: 'Event Name',
        name: 'eventName',
        allowBlank: false,
        emptyText: 'Enter event name...'
    }, {
        xtype: 'flatpickrdatefield',
        fieldLabel: 'Event Date',
        name: 'eventDate',
        allowBlank: false,
        emptyText: 'Select date...',
        minDate: new Date()
    }, {
        xtype: 'fieldcontainer',
        fieldLabel: 'Time',
        layout: 'hbox',
        defaults: {
            flex: 1
        },
        items: [{
            xtype: 'flatpickrtimefield',
            name: 'startTime',
            emptyText: 'Start time...',
            margin: '0 5 0 0',
            flatpickrConfig: {
                minTime: '08:00',
                maxTime: '22:00'
            }
        }, {
            xtype: 'flatpickrtimefield',
            name: 'endTime',
            emptyText: 'End time...',
            flatpickrConfig: {
                minTime: '08:00',
                maxTime: '22:00'
            }
        }]
    }, {
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Full DateTime',
        name: 'fullDateTime',
        emptyText: 'Or select full datetime...'
    }, {
        xtype: 'textarea',
        fieldLabel: 'Notes',
        name: 'notes',
        emptyText: 'Additional notes...'
    }],
    buttons: [{
        text: 'Reset',
        handler: function() {
            this.up('form').getForm().reset();
        }
    }, {
        text: 'Submit',
        formBind: true,
        handler: function() {
            var form = this.up('form').getForm();
            if (form.isValid()) {
                var values = form.getValues();
                console.log('Form values:', values);
                Ext.Msg.alert('Success', 'Booking submitted!\n' + JSON.stringify(values, null, 2));
            }
        }
    }]
});
```

---

## 10. Grid Editor Integration

### Editable Grid with DateTime Column

```javascript
// Define the FlatpickrEditor for grid cells
Ext.define('Ext.ux.FlatpickrEditor', {
    extend: 'Ext.grid.CellEditor',
    alias: 'widget.flatpickreditor',

    field: {
        xtype: 'flatpickrdatetimefield'
    }
});

// Create sample data store
var store = Ext.create('Ext.data.Store', {
    fields: ['id', 'name', 'scheduledDate', 'scheduledTime'],
    data: [
        { id: 1, name: 'Meeting 1', scheduledDate: '2024-01-15', scheduledTime: '09:00' },
        { id: 2, name: 'Meeting 2', scheduledDate: '2024-01-16', scheduledTime: '14:00' },
        { id: 3, name: 'Meeting 3', scheduledDate: '2024-01-17', scheduledTime: '10:30' }
    ]
});

// Create editable grid
Ext.create('Ext.grid.Panel', {
    title: 'Scheduled Events',
    renderTo: Ext.getBody(),
    width: 600,
    height: 300,
    store: store,
    plugins: {
        ptype: 'cellediting',
        clicksToEdit: 1
    },
    columns: [{
        text: 'ID',
        dataIndex: 'id',
        width: 50
    }, {
        text: 'Event Name',
        dataIndex: 'name',
        flex: 1,
        editor: {
            xtype: 'textfield'
        }
    }, {
        text: 'Date',
        dataIndex: 'scheduledDate',
        width: 120,
        editor: {
            xtype: 'flatpickrdatefield'
        }
    }, {
        text: 'Time',
        dataIndex: 'scheduledTime',
        width: 100,
        editor: {
            xtype: 'flatpickrtimefield'
        }
    }]
});
```

---

## 11. Advanced Configuration

### Example 1: Inline Calendar

```javascript
Ext.create('Ext.panel.Panel', {
    title: 'Inline DateTime Picker',
    renderTo: Ext.getBody(),
    width: 350,
    bodyPadding: 10,
    items: [{
        xtype: 'component',
        itemId: 'inlinePicker',
        html: '<div id="inline-flatpickr"></div>',
        listeners: {
            afterrender: function(cmp) {
                var fp = flatpickr('#inline-flatpickr', {
                    inline: true,
                    enableTime: true,
                    dateFormat: 'Y-m-d H:i',
                    time_24hr: true,
                    onChange: function(selectedDates, dateStr) {
                        console.log('Selected:', dateStr);
                        // Update a display field or model
                    }
                });
                cmp.flatpickrInstance = fp;
            },
            destroy: function(cmp) {
                if (cmp.flatpickrInstance) {
                    cmp.flatpickrInstance.destroy();
                }
            }
        }
    }, {
        xtype: 'displayfield',
        itemId: 'selectedDisplay',
        fieldLabel: 'Selected',
        value: 'None'
    }]
});
```

### Example 2: Disable Weekends

```javascript
Ext.create('Ext.form.Panel', {
    title: 'Weekdays Only',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Business Day',
        name: 'datetime',
        emptyText: 'Select weekday...',
        flatpickrConfig: {
            disable: [
                function(date) {
                    return date.getDay() === 0 || date.getDay() === 6;
                }
            ]
        }
    }]
});
```

### Example 3: Disable Specific Dates

```javascript
Ext.create('Ext.form.Panel', {
    title: 'Avoid Holidays',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Appointment',
        name: 'datetime',
        emptyText: 'Select date...',
        flatpickrConfig: {
            disable: [
                '2024-12-25',  // Christmas
                '2024-01-01',  // New Year
                {
                    from: '2024-12-24',
                    to: '2024-12-26'
                }
            ]
        }
    }]
});
```

### Example 4: Week Numbers

```javascript
Ext.create('Ext.form.Panel', {
    title: 'With Week Numbers',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Select Week',
        name: 'datetime',
        emptyText: 'Choose date...',
        flatpickrConfig: {
            weekNumbers: true
        }
    }]
});
```

### Example 5: Localization

```html
<!-- Include locale file -->
<script src="https://cdn.jsdelivr.net/npm/flatpickr/dist/l10n/de.js"></script>
```

```javascript
Ext.create('Ext.form.Panel', {
    title: 'German Locale',
    renderTo: Ext.getBody(),
    width: 400,
    bodyPadding: 10,
    items: [{
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Datum & Zeit',
        name: 'datetime',
        emptyText: 'Datum wählen...',
        dateFormat: 'd.m.Y H:i',
        flatpickrConfig: {
            locale: flatpickr.l10ns.de
        }
    }]
});
```

---

## 12. Complete Working Example

### Full HTML File with ExtJS-style Vanilla JS

Since ExtJS requires a commercial license, here's a complete working example using vanilla JavaScript with ExtJS-style patterns:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ExtJS-Style Flatpickr DateTime Picker</title>

    <!-- Flatpickr CSS -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/themes/airbnb.css">

    <style>
        * { box-sizing: border-box; }
        body {
            font-family: 'Segoe UI', Tahoma, sans-serif;
            margin: 0;
            padding: 20px;
            background: linear-gradient(135deg, #86bc40 0%, #5a8c2a 100%);
            min-height: 100vh;
        }
        .container { max-width: 800px; margin: 0 auto; }
        h1 { color: white; text-align: center; margin-bottom: 30px; }

        /* ExtJS-style panel */
        .x-panel {
            background: white;
            border-radius: 4px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.2);
            margin-bottom: 20px;
        }
        .x-panel-header {
            background: #86bc40;
            color: white;
            padding: 10px 15px;
            font-weight: 600;
            border-radius: 4px 4px 0 0;
        }
        .x-panel-body {
            padding: 15px;
        }

        /* ExtJS-style form */
        .x-form-item {
            margin-bottom: 15px;
        }
        .x-form-item-label {
            display: block;
            margin-bottom: 5px;
            font-weight: 600;
            color: #333;
            font-size: 13px;
        }
        .x-form-field {
            width: 100%;
            padding: 8px 10px;
            border: 1px solid #b5b5b5;
            border-radius: 3px;
            font-size: 13px;
        }
        .x-form-field:focus {
            outline: none;
            border-color: #86bc40;
            box-shadow: 0 0 0 1px #86bc40;
        }

        .x-form-row {
            display: flex;
            gap: 15px;
        }
        .x-form-row .x-form-item {
            flex: 1;
        }

        .x-form-display {
            background: #f5f5f5;
            padding: 15px;
            border-radius: 4px;
            border: 1px solid #ddd;
            margin-top: 15px;
        }
        .x-form-display p {
            margin: 6px 0;
            font-family: monospace;
            font-size: 12px;
        }
        .x-form-display strong {
            color: #86bc40;
            min-width: 120px;
            display: inline-block;
        }

        .x-btn {
            background: #86bc40;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 3px;
            font-weight: 600;
            cursor: pointer;
            margin-right: 10px;
        }
        .x-btn:hover {
            background: #5a8c2a;
        }
        .x-btn-secondary {
            background: #666;
        }
        .x-btn-secondary:hover {
            background: #444;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>ExtJS-Style DateTime Picker</h1>

        <div class="x-panel">
            <div class="x-panel-header">Event Booking Form</div>
            <div class="x-panel-body">
                <form id="bookingForm">
                    <div class="x-form-item">
                        <label class="x-form-item-label">Event Name</label>
                        <input type="text" id="eventName" class="x-form-field" placeholder="Enter event name...">
                    </div>

                    <div class="x-form-row">
                        <div class="x-form-item">
                            <label class="x-form-item-label">Event Date</label>
                            <input type="text" id="eventDate" class="x-form-field" placeholder="Select date...">
                        </div>
                        <div class="x-form-item">
                            <label class="x-form-item-label">Full DateTime</label>
                            <input type="text" id="fullDateTime" class="x-form-field" placeholder="Select datetime...">
                        </div>
                    </div>

                    <div class="x-form-row">
                        <div class="x-form-item">
                            <label class="x-form-item-label">Start Time</label>
                            <input type="text" id="startTime" class="x-form-field" placeholder="Start time...">
                        </div>
                        <div class="x-form-item">
                            <label class="x-form-item-label">End Time</label>
                            <input type="text" id="endTime" class="x-form-field" placeholder="End time...">
                        </div>
                    </div>

                    <div class="x-form-row">
                        <div class="x-form-item">
                            <label class="x-form-item-label">Date Range</label>
                            <input type="text" id="dateRange" class="x-form-field" placeholder="Select range...">
                        </div>
                        <div class="x-form-item">
                            <label class="x-form-item-label">Multiple Dates</label>
                            <input type="text" id="multipleDates" class="x-form-field" placeholder="Select dates...">
                        </div>
                    </div>

                    <div class="x-form-item">
                        <label class="x-form-item-label">DateTime with Seconds</label>
                        <input type="text" id="dateTimeSeconds" class="x-form-field" placeholder="Select datetime with seconds...">
                    </div>

                    <div style="margin-top: 20px;">
                        <button type="button" class="x-btn" onclick="getFormValues()">Get Values</button>
                        <button type="button" class="x-btn x-btn-secondary" onclick="resetForm()">Reset</button>
                    </div>

                    <div class="x-form-display" id="outputPanel">
                        <p><strong>Event Name:</strong> <span id="out-name">-</span></p>
                        <p><strong>Event Date:</strong> <span id="out-date">-</span></p>
                        <p><strong>Full DateTime:</strong> <span id="out-datetime">-</span></p>
                        <p><strong>Start Time:</strong> <span id="out-start">-</span></p>
                        <p><strong>End Time:</strong> <span id="out-end">-</span></p>
                        <p><strong>Date Range:</strong> <span id="out-range">-</span></p>
                        <p><strong>Multiple Dates:</strong> <span id="out-multiple">-</span></p>
                        <p><strong>DateTime+Seconds:</strong> <span id="out-seconds">-</span></p>
                    </div>
                </form>
            </div>
        </div>

        <!-- ExtJS Code Reference -->
        <div class="x-panel">
            <div class="x-panel-header">ExtJS Equivalent Code</div>
            <div class="x-panel-body">
                <pre style="background: #1e1e1e; color: #d4d4d4; padding: 15px; border-radius: 4px; overflow-x: auto; font-size: 12px;">
Ext.create('Ext.form.Panel', {
    title: 'Event Booking',
    width: 500,
    bodyPadding: 15,
    items: [{
        xtype: 'flatpickrdatefield',
        fieldLabel: 'Event Date',
        name: 'eventDate',
        minDate: new Date()
    }, {
        xtype: 'flatpickrdatetimefield',
        fieldLabel: 'Full DateTime',
        name: 'fullDateTime',
        minuteIncrement: 15
    }, {
        xtype: 'flatpickrtimefield',
        fieldLabel: 'Start Time',
        name: 'startTime'
    }],
    buttons: [{
        text: 'Submit',
        handler: function() {
            var values = this.up('form').getValues();
            console.log(values);
        }
    }]
});
                </pre>
            </div>
        </div>
    </div>

    <!-- Flatpickr JS -->
    <script src="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.js"></script>

    <script>
        // Store picker instances (like ExtJS component references)
        var pickers = {};

        // Initialize all pickers on DOM ready
        document.addEventListener('DOMContentLoaded', function() {
            // Date field (like xtype: 'flatpickrdatefield')
            pickers.eventDate = flatpickr('#eventDate', {
                dateFormat: 'Y-m-d',
                minDate: 'today',
                onChange: updateOutput
            });

            // DateTime field (like xtype: 'flatpickrdatetimefield')
            pickers.fullDateTime = flatpickr('#fullDateTime', {
                enableTime: true,
                dateFormat: 'Y-m-d H:i',
                time_24hr: true,
                minuteIncrement: 5,
                onChange: updateOutput
            });

            // Time fields (like xtype: 'flatpickrtimefield')
            pickers.startTime = flatpickr('#startTime', {
                enableTime: true,
                noCalendar: true,
                dateFormat: 'H:i',
                time_24hr: true,
                minuteIncrement: 15,
                minTime: '08:00',
                maxTime: '20:00',
                onChange: function(dates, dateStr) {
                    // ExtJS-style: endField.setMinValue(val)
                    pickers.endTime.set('minTime', dateStr);
                    updateOutput();
                }
            });

            pickers.endTime = flatpickr('#endTime', {
                enableTime: true,
                noCalendar: true,
                dateFormat: 'H:i',
                time_24hr: true,
                minuteIncrement: 15,
                minTime: '08:00',
                maxTime: '20:00',
                onChange: updateOutput
            });

            // Range picker
            pickers.dateRange = flatpickr('#dateRange', {
                mode: 'range',
                dateFormat: 'Y-m-d',
                onChange: updateOutput
            });

            // Multiple dates
            pickers.multipleDates = flatpickr('#multipleDates', {
                mode: 'multiple',
                dateFormat: 'Y-m-d',
                conjunction: ', ',
                onChange: updateOutput
            });

            // DateTime with seconds
            pickers.dateTimeSeconds = flatpickr('#dateTimeSeconds', {
                enableTime: true,
                enableSeconds: true,
                dateFormat: 'Y-m-d H:i:S',
                time_24hr: true,
                onChange: updateOutput
            });

            console.log('All Flatpickr instances initialized');
        });

        // Get form values (like form.getValues() in ExtJS)
        function getFormValues() {
            var values = {
                eventName: document.getElementById('eventName').value,
                eventDate: document.getElementById('eventDate').value,
                fullDateTime: document.getElementById('fullDateTime').value,
                startTime: document.getElementById('startTime').value,
                endTime: document.getElementById('endTime').value,
                dateRange: document.getElementById('dateRange').value,
                multipleDates: document.getElementById('multipleDates').value,
                dateTimeSeconds: document.getElementById('dateTimeSeconds').value
            };

            console.log('Form values:', values);
            updateOutput();
            return values;
        }

        // Reset form (like form.reset() in ExtJS)
        function resetForm() {
            document.getElementById('eventName').value = '';
            Object.keys(pickers).forEach(function(key) {
                pickers[key].clear();
            });
            updateOutput();
        }

        // Update output display
        function updateOutput() {
            document.getElementById('out-name').textContent =
                document.getElementById('eventName').value || '-';
            document.getElementById('out-date').textContent =
                document.getElementById('eventDate').value || '-';
            document.getElementById('out-datetime').textContent =
                document.getElementById('fullDateTime').value || '-';
            document.getElementById('out-start').textContent =
                document.getElementById('startTime').value || '-';
            document.getElementById('out-end').textContent =
                document.getElementById('endTime').value || '-';
            document.getElementById('out-range').textContent =
                document.getElementById('dateRange').value || '-';
            document.getElementById('out-multiple').textContent =
                document.getElementById('multipleDates').value || '-';
            document.getElementById('out-seconds').textContent =
                document.getElementById('dateTimeSeconds').value || '-';
        }
    </script>
</body>
</html>
```

---

## Quick Reference: ExtJS xtype to Flatpickr Config

| ExtJS xtype | Flatpickr Config |
|-------------|------------------|
| `datefield` | `{ enableTime: false }` |
| `timefield` | `{ enableTime: true, noCalendar: true }` |
| `datetimefield` | `{ enableTime: true }` |

---

## Common Configuration Options

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
| `weekNumbers` | boolean | Show week numbers |
| `disable` | array | Dates/functions to disable |
| `locale` | object | Localization settings |

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
