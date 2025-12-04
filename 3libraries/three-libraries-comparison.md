# Datepicker Library Comparison
## Flatpickr vs Tempus Dominus vs Air Datepicker

---

## Quick Summary

| Criteria | Flatpickr | Tempus Dominus | Air Datepicker |
|----------|:---------:|:--------------:|:--------------:|
| **Dependencies** | ✅ Zero | ❌ Popper.js required | ✅ Zero |
| **Bundle Size** | ~15kb | ~25kb + Popper | ~13kb |
| **Maintenance** | ⚠️ Stagnant | ✅ Active | ✅ Active |
| **Angular Wrapper** | ✅ ng2-flatpickr | ❌ Manual | ❌ Manual |
| **Timezone Support** | ❌ None | ⚠️ v5 only | ❌ None |
| **License** | MIT | MIT | MIT |

**Suggestion: Air Datepicker** - Smallest, actively maintained, zero dependencies

---

## 1. Framework Compatibility

### AngularJS / ExtJS
All three require **manual integration** via custom directives/components.

### Angular (Latest)
| Library | Wrapper | Reactive Forms |
|---------|---------|----------------|
| Flatpickr | `ng2-flatpickr`, `ngx-flatpickr-wrapper` | ✅ Yes |
| Tempus Dominus | None (manual) | Manual |
| Air Datepicker | None (manual) | Manual |

```javascript
// Flatpickr Angular Example
import { Ng2FlatpickrModule } from 'ng2-flatpickr';
```

---

## 2. Dependencies & Architecture

| Library | Type | Dependencies |
|---------|------|--------------|
| **Flatpickr** | Pure JS | None |
| **Tempus Dominus v6** | TypeScript | Popper.js (required), Font Awesome (optional) |
| **Air Datepicker v3** | ES6 | None |

```javascript
// Air Datepicker - Zero deps
import AirDatepicker from 'air-datepicker';
new AirDatepicker('#el');

// Tempus Dominus - Requires Popper
import { TempusDominus } from '@eonasdan/tempus-dominus';
// Must load Popper.js first!
```

---

## 3. Localization

All three support **40+ locales** with custom locale objects.

```javascript
// Flatpickr
import Russian from 'flatpickr/dist/l10n/ru.js';
flatpickr("#el", { locale: Russian.ru });

// Air Datepicker
import localeEn from 'air-datepicker/locale/en';
new AirDatepicker('#el', { locale: localeEn });

// Tempus Dominus
new TempusDominus(el, { localization: { locale: 'es' } });
```

### Custom Locale
```javascript
// All three accept custom locale objects
const customLocale = {
  days: ['Sunday', 'Monday', ...],
  months: ['January', 'February', ...],
  dateFormat: 'MM/dd/yyyy',
  firstDay: 1  // Monday
};
```

---

## 4. Date/Timestamp Handling

All three accept:
- ✅ `Date` objects
- ✅ Timestamps (milliseconds)
- ✅ Date strings

```javascript
// All libraries
new AirDatepicker('#el', {
  selectedDates: [
    new Date(),              // Date object
    1626307200000,           // Timestamp
    '2024-01-15'             // String
  ]
});
```

---

## 5. Theming

| Library | CSS Variables | Built-in Themes | SASS |
|---------|:-------------:|:---------------:|:----:|
| Flatpickr | ❌ | 7+ (material, airbnb, dark) | ✅ |
| Tempus Dominus | ✅ | 3 (light, dark, auto) | ✅ |
| **Air Datepicker** | ✅ | Custom via CSS vars | ✅ |

```css
/* Air Datepicker - Easy theming with CSS variables */
.air-datepicker {
  --adp-color: #4a4a4a;
  --adp-background-color: #fff;
  --adp-accent-color: #1976d2;
}
```

---

## 6. Positioning (appendTo)

| Library | Option | Syntax |
|---------|--------|--------|
| Flatpickr | `appendTo` | `appendTo: document.querySelector('.modal')` |
| Tempus Dominus | `container` | `container: document.getElementById('modal')` |
| Air Datepicker | `container` | `container: '.modal-body'` or `HTMLElement` |

```javascript
// Air Datepicker - Also supports custom positioning function
new AirDatepicker('#el', {
  position({ $datepicker, $target, $pointer }) {
    // Full manual control
    $datepicker.style.left = '100px';
    $datepicker.style.top = '200px';
  }
});
```

---

## 7. CSP (Content Security Policy)

| Library | Inline Styles | unsafe-inline Required |
|---------|:-------------:|:----------------------:|
| Flatpickr | ⚠️ Yes | Yes (or hash) |
| Tempus Dominus | ⚠️ Yes | Yes (or hash) |
| Air Datepicker | ⚠️ Less (CSS vars) | Minimal |

**Air Datepicker** is most CSP-friendly due to CSS variables approach.

---

## 8. Timezone Support

### ❌ None have built-in timezone display

| Feature | Flatpickr | Tempus Dominus | Air Datepicker |
|---------|:---------:|:--------------:|:--------------:|
| Timezone option | ❌ | ⚠️ v5 only (deprecated) | ❌ |
| Display TZ abbreviation | ❌ | ❌ | ❌ |
| UTC mode | ✅ `dateFormat: "Z"` | ✅ | ✅ |

### Found one work around to display timezone: Luxon
```javascript
import { DateTime } from 'luxon';

new AirDatepicker('#el', {
  dateFormat: (date) => {
    return DateTime.fromJSDate(date)
      .setZone('America/New_York')
      .toFormat('yyyy-MM-dd HH:mm ZZZZ'); // 2024-01-15 14:30 EST
  }
});
```

---

## 9. Maintenance Status

| Library | Last Release | GitHub Stars | Status |
|---------|--------------|--------------|--------|
| Flatpickr | ~2 years ago | 16.4k | ⚠️ Stagnant (659+ open issues) |
| Tempus Dominus | Active (v6.10.4) | 7k | ✅ Active |
| Air Datepicker | 6 months ago (v3.6.0) | 3.5k | ✅ Active |

---

## 10. Licensing

**All three: MIT License**
- ✅ Commercial use
- ✅ Modification
- ✅ Distribution
- ✅ Private use

---

## Integration Examples

### AngularJS Directive
```javascript
angular.module('app').directive('datepicker', function() {
  return {
    restrict: 'A',
    require: 'ngModel',
    link: function(scope, el, attrs, ngModel) {
      const picker = new AirDatepicker(el[0], {
        timepicker: true,
        onSelect: ({date}) => {
          scope.$apply(() => ngModel.$setViewValue(date));
        }
      });
      scope.$on('$destroy', () => picker.destroy());
    }
  };
});
```

### Angular Component
```typescript
@Component({
  selector: 'app-datepicker',
  template: `<input #input type="text">`
})
export class DatepickerComponent implements OnInit, OnDestroy {
  @ViewChild('input') input: ElementRef;
  private picker: AirDatepicker;

  ngOnInit() {
    this.picker = new AirDatepicker(this.input.nativeElement, {
      timepicker: true,
      locale: localeEn
    });
  }

  ngOnDestroy() {
    this.picker?.destroy();
  }
}
```

### ExtJS Component
```javascript
Ext.define('App.DatePicker', {
  extend: 'Ext.form.field.Text',
  alias: 'widget.airdatepicker',
  
  initComponent: function() {
    this.callParent();
    this.on('afterrender', () => {
      this.picker = new AirDatepicker(this.inputEl.dom, {
        timepicker: this.timepicker,
        onSelect: ({date}) => this.setValue(date)
      });
    });
  }
});
```

---

## Recommendation

### 🏆 **Air Datepicker** - As per my analysis Best Overall Choice

| Reason | Benefit |
|--------|---------|
| Zero dependencies | Cleaner integration, smaller bundle |
| Smallest size (~13kb) | Faster load times |
| CSS variables | Easiest theming, better CSP |
| Active maintenance | Regular updates, bug fixes |
| Modern ES6 | Better code quality |

---

---