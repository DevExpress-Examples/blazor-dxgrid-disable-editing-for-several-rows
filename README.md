<!-- default badges list -->
![](https://img.shields.io/endpoint?url=https://codecentral.devexpress.com/api/v1/VersionRange/520828852/22.1.4%2B)
[![](https://img.shields.io/badge/Open_in_DevExpress_Support_Center-FF7200?style=flat-square&logo=DevExpress&logoColor=white)](https://supportcenter.devexpress.com/ticket/details/T1106833)
[![](https://img.shields.io/badge/📖_How_to_use_DevExpress_Examples-e9f6fc?style=flat-square)](https://docs.devexpress.com/GeneralInformation/403183)
<!-- default badges end -->
# Grid for Blazor - How to disable row editing depending on row values

This example disables an **Edit** button for [DxGrid](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxGrid) rows whose **Date** field value is less than or equal to the current date.

![Blazor DxGrid disable row editing](images/disable-row-editing.png)

## Overview

Follow the steps below to allow editing only the rows whose field values match your condition.

### 1. Display an Edit button

Declare the [DxGridCommandColumn](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxGridCommandColumn) object in the [Columns](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxGrid.Columns) template to display a command column. The command column displays predefined **New**, **Edit**, and **Delete** command buttons. Define the column's [CellDisplayTemplate](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxGridCommandColumn.CellDisplayTemplate) to implement a custom **Edit** [button](https://docs.devexpress.com/Blazor/401581/buttons). 

```razor
<DxGrid @ref="myGrid" Data="@forecasts">
    <Columns>
        <DxGridCommandColumn NewButtonVisible=false>
            <CellDisplayTemplate>
                <DxButton Text="Edit"/>
            </CellDisplayTemplate>
        </DxGridCommandColumn>
    </Columns>
</DxGrid>
```

### 2. Customize the Edit button

Use the [CellDisplayTemplate](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxGridCommandColumn.CellDisplayTemplate)'s [context](https://docs.devexpress.com/Blazor/DevExpress.Blazor.GridColumnCellDisplayTemplateContext) parameter to access the [grid object](https://docs.devexpress.com/Blazor/DevExpress.Blazor.GridColumnCellDisplayTemplateContext.Grid) and proccesed [data item](https://docs.devexpress.com/Blazor/DevExpress.Blazor.GridColumnCellDisplayTemplateContext.DataItem). Set the **Edit** button's [Enabled](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxButton.Enabled) property to `true` or `false` depending on the data item's field values. Call the grid's [StartEditDataItemAsync](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxGrid.StartEditDataItemAsync(System.Object)) method to display an edit form when a user [clicks](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxButton.Click) the **Edit** button.

```razor
<CellDisplayTemplate>
    @{
        var date = context.DataItem != null ? (context.DataItem as WeatherForecast).Date : DateTime.Now;
            <DxButton Text="Edit"
                      Click="() => myGrid.StartEditDataItemAsync(context.DataItem)" 
                      Enabled="@(date <= DateTime.Now ? false : true)" />
    }
</CellDisplayTemplate>
```

### 3. Define an edit form's template

The default edit form shows only the predefined **Save** and **Cancel** buttons. Use the [EditFormTemplate](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxGrid.EditFormTemplate) to define the edit form content. This example uses the [DxFormLayout](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxFormLayout) to create an edit form that displays [DevExpress editors](https://docs.devexpress.com/Blazor/401156/data-editors) for all editable data columns.

```razor
<EditFormTemplate Context="editFormContext">
    @{ var forecast = (WeatherForecast)editFormContext.EditModel; }
    <DxFormLayout>
        <DxFormLayoutItem Caption="Date:">
            <DxDateEdit @bind-Date="@forecast.Date"/>
        </DxFormLayoutItem>
        <DxFormLayoutItem Caption="Summary:">
            <DxComboBox Data="@ForecastService.Summaries" @bind-Value="@forecast.Summary"/>
        </DxFormLayoutItem>
        <DxFormLayoutItem Caption="Temperature (C):">
            <DxSpinEdit @bind-Value="@forecast.TemperatureC"/>
        </DxFormLayoutItem>
        <DxFormLayoutItem Caption="Temperature (F):">
            @forecast.TemperatureF.ToString()
        </DxFormLayoutItem>
    </DxFormLayout>
</EditFormTemplate>
```

### 4. Save changes and reload data

The [EditModelSaving](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxGrid.EditModelSaving) event fires when a user submits an edit form and allows you to save changes. Use the following event argument's properties to post changes to the data source:

- The [EditModel](https://docs.devexpress.com/Blazor/DevExpress.Blazor.GridEditModelSavingEventArgs.EditModel) property returns the edit model that stores all changes.
- The [DataItem](https://docs.devexpress.com/Blazor/DevExpress.Blazor.GridEditModelSavingEventArgs.DataItem) property returns the proccesed data item.

[Reload](https://docs.devexpress.com/Blazor/DevExpress.Blazor.DxGrid.Reload) grid data after you post updates to the data source.

```razor
<DxGrid @ref="myGrid" Data="@forecasts" EditModelSaving="OnEditModelSaving">
    <!-- ... -->
</DxGrid>

@code {
    void OnEditModelSaving(GridEditModelSavingEventArgs e) {
        WeatherForecast dataItem = e.DataItem as WeatherForecast;
        WeatherForecast editModel = e.EditModel as WeatherForecast;
        dataItem.Date = editModel.Date;
        dataItem.TemperatureC = editModel.TemperatureC;
        dataItem.Summary = editModel.Summary;
        myGrid.Reload();
    }
}
```

## Files to Look At

- [Index.razor](./CS/DisableEditButton/Pages/Index.razor)

## Documentation

- [Grid: Bind to Data](https://docs.devexpress.com/Blazor/403737/grid/bind-to-data)
- [Grid: Edit Data and Validate Input](https://docs.devexpress.com/Blazor/403454/grid/edit-data-and-validate-input)

## More Examples

- [Grid for Blazor - How to edit a row on a separate page](https://github.com/DevExpress-Examples/blazor-DxGrid-Separate-Edit-Form)
- [Grid for Blazor - How to display a custom confirmation dialog](https://github.com/DevExpress-Examples/blazor-dxgrid-show-custom-confirmation-dialog)
