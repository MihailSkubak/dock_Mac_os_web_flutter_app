This code is a Flutter app that implements a dock interface with drag and drop functionality.

Main components of the app

main(): The entry point to the app that launches MyApp.
void main() { runApp(const MyApp()); }

MyApp: The main widget of the app that creates a materialized app with a single main page.
class MyApp extends StatelessWidget { const MyApp({super.key});

@override Widget build(BuildContext context) { return MaterialApp( home: Scaffold( body: Center( child: Dock( items: const [ Icons.person, Icons.message, Icons.call, Icons.camera, Icons.photo, ], builder: (e) { return AnimatedContainer( duration: const Duration(seconds: 1), constraints: const BoxConstraints(minWidth: 48), height: 48, margin: const EdgeInsets.all(8), decoration: BoxDecoration( borderRadius: BorderRadius.circular(8), color: Colors.primaries[e.hashCode % Colors.primaries.length], ), child: Center(child: Icon(e, color: Colors.white)), ); }, ), ), ), ); } }

Dock

3.Dock: Widget implementing dock functionality with drag and drop and reordering of elements.

class Dock extends StatefulWidget { const Dock({ super.key, this.items = const [], required this.builder, });

final List items; // Список элементов final Widget Function(T) builder; // Функция построения виджета для элемента

@override State<Dock> createState() => _DockState(); }

Dock state

_DockState: State of Dock, manages list of items using ValueNotifier.
class _DockState extends State<Dock> { late final ValueNotifier<List> _itemsNotifier;

@override void initState() { super.initState(); _itemsNotifier = ValueNotifier<List>(widget.items.toList()); }

@override void dispose() { _itemsNotifier.dispose(); super.dispose(); }

@override Widget build(BuildContext context) { return Container( height: 75, decoration: BoxDecoration( borderRadius: BorderRadius.circular(8), color: Colors.black12, ), padding: const EdgeInsets.all(4), child: ValueListenableBuilder<List>( valueListenable: _itemsNotifier, builder: (context, items, child) { return Row( mainAxisSize: MainAxisSize.min, children: items.asMap().entries.map((entry) { int index = entry.key; // Element index T item = entry.value; // Element

return DragTarget( onAccept: (receivedItem) { final fromIndex = items.indexOf(receivedItem); if (fromIndex != -1) { // Move the item items.removeAt(fromIndex); items.insert(index, receivedItem); _itemsNotifier.value = List.from(items); } }, builder: (context, candidateData, rejectedData) { return Draggable( data: item, feedback: widget.builder(item), // Widget when dragging childWhenDragging: Container(), // When the item is being dragged child: DockItem(item: item, builder: widget.builder), ); }, ); }).toList(), ); }, ), ); } }

Dock item

DockItem: Widget representing a single dock item that allows zooming on mouse hover.
class DockItem extends StatefulWidget { final T item; final Widget Function(T) builder;

const DockItem({Key? key, required this.item, required this.builder}) : super(key: key);

@override _DockItemState createState() => _DockItemState(); }

class _DockItemState extends State<DockItem> { double scale = 1.0;

@override Widget build(BuildContext context) { return MouseRegion( onEnter: () => setState(() => scale = 1.1), // Zoom in onExit: () => setState(() => scale = 1.0), // Reset scale child: Transform.scale( scale: scale, child: widget.builder(widget.item), // Building an Element Widget ), ); } }

General Functionality

The Dock implements a drag-and-drop interface that allows users to reorder items in the Dock. Each item is an icon that can resize on mouse hover, improving the user experience. Using ValueNotifier allows the UI to update when the state changes.

Conclusion

This code creates a simple but functional Flutter app with items that can be dragged and reordered. The Dock uses ValueNotifier to manage the state of the list of items, and Draggable and DragTarget to implement the drag functionality.
