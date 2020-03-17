# Date picker range

This is a Datepicker.Range addition to customized MooTools [date picker][1] package integrated in
[Contao Open Source CMS][2].

### Usage example:

*dca/tl_calendar_events.php:*
```php
// Remove the default datepicker
$GLOBALS['TL_DCA']['tl_iso_product']['fields']['startDate']['eval']['datepicker'] = false;

// Add customized version
$GLOBALS['TL_DCA']['tl_iso_product']['fields']['startDate']['wizard'] = array(array('tl_calendar_events', 'datePickerRangeWizard'));

class tl_calendar_events extends Backend {

    public function datePickerRangeWizard(DataContainer $dc) {
        $arrData = $GLOBALS['TL_DCA'][$dc->table]['fields'][$dc->field];
        $rgxp = $arrData['eval']['rgxp'];
        $format = Date::formatToJs(Config::get($rgxp . 'Format'));

        $GLOBALS['TL_JAVASCRIPT'][] = 'assets/datepicker-range/js/datepicker-range.min.js';
        $GLOBALS['TL_CSS'][] = 'assets/datepicker-range/css/datepicker-range.min.css';

        $strOnSelect = '';

        // Trigger the auto-submit function (see #8603)
        if ($arrData['eval']['submitOnChange'])
        {
            $strOnSelect = ",\n        onSelect: function() { Backend.autoSubmit(\"" . $dc->table . "\"); }";
        }

        return ' ' . Image::getHtml('assets/datepicker/images/icon.svg', '', 'title="' . StringUtil::specialchars($GLOBALS['TL_LANG']['MSC']['datepicker']) . '" id="toggle_' . $dc->field . '" style="cursor:pointer"') . '
    <script>
    window.addEvent("domready", function() {
      new Picker.Date.Range($("ctrl_' . $dc->field . '"), {
        endDateField: $("ctrl_endDate"),
        draggable: false,
        toggle: $("toggle_' . $dc->field . '"),
        format: "' . $format . '",
        positionOffset: {x:-211,y:-209},
        pickerClass: "datepicker_bootstrap",
        columns: 1,
        getStartEndDate: function(input) {
            return [input.get("value"), this.options.endDateField.get("value")].map(function(date){
            var parsed = Date.parse(date);
            return Date.isValid(parsed) ? parsed : null;
          }).clean();
        },
        setStartEndDate: function(input, dates){
          input.set("value", dates[0].format(this.options.format));
          this.options.endDateField.set("value", dates[1].format(this.options.format));
        },
        useFadeInOut: !Browser.ie' . $strOnSelect . ',
        startDay: ' . $GLOBALS['TL_LANG']['MSC']['weekOffset'] . ',
        titleFormat: "' . $GLOBALS['TL_LANG']['MSC']['titleFormat'] . '"
      });
    });
    </script>';
    }
}
```


[1]: https://github.com/arian/mootools-datepicker/
[2]: https://contao.org
