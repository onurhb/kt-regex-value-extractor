
# Kotlin regex string interpolation
Create interpolation string and automatically get it replaced with values from Regex match:

``` kotlin
fun main(args: Array<String>) {
    val pattern = "(?<year>\\d{4})-(?<month>\\d{1,2})(-(?<day>\\d{1,2}))?".toRegex()

    val match = pattern.matchEntire("1992-1-3")!!
    val result = RegexValueExtractor.interpolateString(
        template = "year: {year}, month: {month}( and maybe day: {day})",
        groups = match.groups
    ) { parameter, value ->
        if (parameter == "month" || parameter == "day") {
            if (value?.length == 1) "0$value"
            else value
        }
        else value
    }

    // prints: 'year: 1992, month: 01 and maybe day: 03'
    println(result)
}
```
_See tests for more examples_
# Interpolation rules
- Wrap group names with brackets: `{day}`
- Wrap any optional substring with parentheses if it contains optional parameter(s), e.g. `(maybe day: {day})` will erase everything inside the parentheses if `{day}` is not matched
- Default if a parameter is not found `{day:01}`, will output `01` if `{day}` is not matched
- Multiple parameters with `|` separator, such as `{day|anotherday}`, will try to find `day` first
