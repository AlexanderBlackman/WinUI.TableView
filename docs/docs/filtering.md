# Filtering

`TableView` provides an Excel-like column filter flyout that lets users select which values to show in each column. Filtering is built in and requires no extra code for the standard case.

## When to use it

Use filtering when users need to narrow down a large list to items that match specific values in one or more columns. For example, filtering a product list to show only items that are "In Stock", or filtering an order list to a specific customer.

## Basic example

Filtering is on by default. Click the options button in a column header to open the filter flyout:

```xml
<tv:TableView ItemsSource="{x:Bind Products}" />
```

## Opening the filter flyout

By default, the column filter flyout opens from the options button in the column header. You can optionally open it with a right-click:

```xml
<tv:TableView UseRightClickForColumnFilter="True" />
```

![Filter flyout showing value checkboxes](../images/filtering-flyout.png)

## Disabling filtering

Disable filtering for the entire table:

```xml
<tv:TableView CanFilterColumns="False" />
```

Disable filtering for a specific column:

```xml
<tv:TableViewTextColumn Header="ID" Binding="{Binding Id}" CanFilter="False" />
```

## Showing item counts in the filter flyout

Show how many rows match each filter value:

```xml
<tv:TableView ShowFilterItemsCount="True" />
```

## Filtering programmatically

Add filter descriptions to `TableView.FilterDescriptions`:

```csharp
// Show only in-stock items
tableView.FilterDescriptions.Add(
    new FilterDescription("InStock", new PredicateFilter(v => v is true)));

// Clear all filters
tableView.ClearAllFilters();
```

## Clearing and refreshing filters programmatically

Use [`ClearAllFilters()`](xref:WinUI.TableView.TableView.ClearAllFilters) to remove all active filter descriptions and reset every column's filter state in one call:

```csharp
tableView.ClearAllFilters();
```

Use [`RefreshFilter()`](xref:WinUI.TableView.TableView.RefreshFilter) to re-evaluate the current filter against the data without user interaction. Call this after modifying source data in-place so the view reflects the latest values:

```csharp
// Re-run the current filter after data changed in-place
tableView.RefreshFilter();
```

## Custom filter handler

The [`FilterHandler`](xref:WinUI.TableView.TableView.FilterHandler) property accepts an `IColumnFilterHandler` implementation. This allows you to replace the built-in filter logic entirely:

```csharp
public class MyFilterHandler : IColumnFilterHandler
{
    // Implement IColumnFilterHandler members
}

tableView.FilterHandler = new MyFilterHandler();
```

## Checking filter state

```csharp
bool isFiltered = tableView.IsFiltered;
// true if any FilterDescription is active OR any column reports IsFiltered = true
```

## Common options

| Property | Description |
|---|---|
| [`CanFilterColumns`](xref:WinUI.TableView.TableView.CanFilterColumns) | Enables or disables filtering for all columns (default `true`) |
| [`CanFilter`](xref:WinUI.TableView.TableViewColumn.CanFilter) | Per-column filter toggle |
| [`IsFiltered`](xref:WinUI.TableView.TableViewColumn.IsFiltered) | `true` if a filter is active on this column |
| [`FilterDescriptions`](xref:WinUI.TableView.TableView.FilterDescriptions) | The collection of active filter descriptions |
| [`IsFiltered`](xref:WinUI.TableView.TableView.IsFiltered) | `true` if any filter is applied to the view |
| [`ClearAllFilters()`](xref:WinUI.TableView.TableView.ClearAllFilters) | Removes all filter descriptions and resets column filter state |
| [`RefreshFilter()`](xref:WinUI.TableView.TableView.RefreshFilter) | Re-evaluates current filter descriptions without user interaction |
| [`ShowFilterItemsCount`](xref:WinUI.TableView.TableView.ShowFilterItemsCount) | Shows the count of matching rows next to each filter value |
| [`UseRightClickForColumnFilter`](xref:WinUI.TableView.TableView.UseRightClickForColumnFilter) | Opens the filter flyout on column header right-click |
| [`FilterHandler`](xref:WinUI.TableView.TableView.FilterHandler) | Custom filter handler implementation |
| [`FilterItemTemplate`](xref:WinUI.TableView.TableViewColumn.FilterItemTemplate) | Per-column `DataTemplate` for the value slot of each filter item |
| [`ShowFilterSearchBox`](xref:WinUI.TableView.TableViewColumn.ShowFilterSearchBox) | Per-column toggle for the filter flyout's search box (default `true`) |

## Customizing filter item display

By default each filter item renders [`TableViewFilterItem.ValueText`](xref:WinUI.TableView.TableViewFilterItem.ValueText), which is `Value?.ToString()`. Set [`FilterItemTemplate`](xref:WinUI.TableView.TableViewColumn.FilterItemTemplate) on a column to render the value differently — for example as an icon, or with a localized label:

```xml
<tv:TableViewComboBoxColumn Header="Priority" Binding="{Binding Priority}">
    <tv:TableViewComboBoxColumn.FilterItemTemplate>
        <DataTemplate x:DataType="tv:TableViewFilterItem">
            <StackPanel Orientation="Horizontal" Spacing="4">
                <FontIcon Glyph="{x:Bind Value, Converter={StaticResource PriorityGlyphConverter}}" />
                <TextBlock Text="{x:Bind Value, Converter={StaticResource PriorityLabelConverter}}" />
            </StackPanel>
        </DataTemplate>
    </tv:TableViewComboBoxColumn.FilterItemTemplate>
</tv:TableViewComboBoxColumn>
```

The template fills the value slot only; the checkbox, the select-all tri-state and the item count remain owned by the flyout. The data context is the [`TableViewFilterItem`](xref:WinUI.TableView.TableViewFilterItem), whose `Value` is the raw filter key — the same object the filter compares against, so the template changes presentation only.

Note that the flyout's search box matches `Value.ToString()`, not the rendered template. If the template shows text that differs from `ToString()` (a localized label, for instance), consider setting `ShowFilterSearchBox="False"` on that column so the box cannot silently fail to match.

## Notes and limitations

- Filtering is applied to the internal collection view. It does not mutate the original collection.
- [`TableViewTemplateColumn`](xref:WinUI.TableView.TableViewTemplateColumn) has `CanFilter = false` by default. Set [`OperationContentBinding`](xref:WinUI.TableView.TableViewColumn.OperationContentBinding) to enable filtering on template columns.
- When a filter is active, new items added to the source collection may not appear in the view until the filter is re-evaluated or live shaping is enabled (`AllowLiveShaping = true`).

## Related articles

- [Sorting](sorting.md)
- [Binding data](binding-data.md)
- [Performance guidance](performance.md)
