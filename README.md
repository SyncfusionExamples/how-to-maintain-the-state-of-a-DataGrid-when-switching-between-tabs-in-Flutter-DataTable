# How to maintain the state of a DataGrid when switching between tabs in Flutter DataTable (SfDataGrid)

When switching between tabs, all the widgets undergo re-creation, causing the [Flutter DataGrid](https://www.syncfusion.com/flutter-widgets/flutter-datagrid) to lose its previous state, including sorting, filtering, and scroll position settings.

## STEP 1:
Initialize the [SfDataGrid](https://pub.dev/documentation/syncfusion_flutter_datagrid/latest/datagrid/SfDataGrid-class.html) widget with all the necessary properties. In order to maintain the state of the [Flutter DataGrid](https://www.syncfusion.com/flutter-widgets/flutter-datagrid), it is important to use the [AutomaticKeepAliveClientMixin](https://api.flutter.dev/flutter/widgets/AutomaticKeepAliveClientMixin-mixin.html). By using this mixin, the Flutter DataGrid can be preserved in its current state. Override the method [wantKeepAlive](https://api.flutter.dev/flutter/widgets/AutomaticKeepAliveClientMixin/wantKeepAlive.html) to return true, ensuring that the state is retained.

```dart
class DataGrid extends StatefulWidget {
  const DataGrid({
    super.key,
  });

  @override
  State<DataGrid> createState() => DataGridState();
}

class DataGridState extends State<DataGrid>
    with AutomaticKeepAliveClientMixin<DataGrid> {
  List<Employee> employees = <Employee>[];
  late EmployeeDataSource employeeDataSource;


  @override
  void initState() {
    super.initState();
    employees = getEmployeeData();
    employeeDataSource = EmployeeDataSource(employeeData: employees);
  }

  @override
  bool get wantKeepAlive => true;

  @override
  Widget build(BuildContext context) {
    super.build(context);
    return SfDataGrid(
      source: employeeDataSource,
      allowSorting: true,
      allowFiltering: true,
      columnWidthMode: ColumnWidthMode.fill,
      columns: getColumns,
    );
  }
}

```
## STEP 2:
Wrap your DataGrid widget in a [TabBarView](https://api.flutter.dev/flutter/material/TabBarView-class.html).

```dart
import 'package:flutter/material.dart';
import 'package:syncfusion_flutter_datagrid/datagrid.dart';

class MyHomePageState extends State<MyHomePage> with TickerProviderStateMixin {
  late TabController _tabController;

  @override
  void initState() {
    super.initState();
    _tabController = TabController(length: 2, vsync: this);
  }

  @override
  void dispose() {
    _tabController.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Syncfusion Flutter DataGrid'),
        bottom: TabBar(
          controller: _tabController,
          tabs: const [
            Tab(text: 'Employees'),
            Tab(text: 'Empty View'),
          ],
        ),
      ),
      body: TabBarView(controller: _tabController, children: const [
        DataGrid(),
        Center(
          child: Text('Empty View'),
        ),
      ]),
    );
  }
}

```