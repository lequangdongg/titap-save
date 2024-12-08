```
import { Slice, Fragment, Node } from '@tiptap/pm/model';

const transformPasted = (slice: Slice, view: EditorView): Slice => {
  const flattenNestedOrderedLists = (node: Node): Node => {
    if (node.type.name === 'orderedList') {
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
      return node.copy(node.content.map(flattenNestedOrderedLists));
    }
    return node;
  };
  const transformedContent = slice.content.content.map(flattenNestedOrderedLists);
  return new Slice(Fragment.from(transformedContent), slice.openStart, slice.openEnd);
};

const editorProps = {
  transformPasted
};

```
