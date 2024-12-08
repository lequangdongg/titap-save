```
import { Slice, Fragment, Node } from 'prosemirror-model';
import { EditorView } from 'prosemirror-view';

const transformPasted = (slice: Slice, view: EditorView): Slice => {
  const flattenNestedOrderedLists = (node: Node): Node => {
    if (node.type.name === 'ordered_list') {
      const newContent: Node[] = [];
      node.content.forEach(child => {
        if (child.type.name !== 'orderedList') {
          newContent.push(child);
        } else {
          child.content.forEach(grandChild => {
            if (grandChild.type.name === 'listItem') {
              newContent.push(grandChild);
            }
          });
        }
      });
      return node.copy(Fragment.from(newContent));
    } else if (node.isBlock) {
      // Apply the transformation recursively for all child nodes
      return node.copy(node.content.map(flattenNestedOrderedLists));
    }
    return node;
  };
  const transformedContent = slice.content.content.map(flattenNestedOrderedLists);
  return new Slice(Fragment.from(transformedContent), slice.openStart, slice.openEnd);
};

const editorProps = {
  transformPasted: (slice: Slice, view: EditorView): Slice => transformPasted(slice, view)
};

// Example EditorView usage
const editorView = new EditorView({
  props: editorProps
});

```
