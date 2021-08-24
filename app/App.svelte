<page>
  <actionBar title="My Tasks" />

  <tabs tabsPosition="bottom">
    <tabStrip>
      <tabStripItem title="To Do" />
      <tabStripItem title="Completed" />
    </tabStrip>

    <tabContentItem>
      <gridLayout columns="*,120" rows="70,*">
        <!-- Configures the text field and ensures that pressing Return on the keyboard
          produces the same result as tapping the button. -->
        <textField col="0" row="0" bind:text="{textFieldValue}" hint="Type new task..." editable="true"
          on:returnPress="{onButtonTap}" />
        <button col="1" row="0" text="Add task" on:tap="{onButtonTap}" />

        <listView items="{todos}" on:itemTap="{onItemTap}" row="1" colSpan="2">
          <Template let:item>
            <label text="{item.name}" textWrap="true" />
          </Template>
        </listView>
      </gridLayout>
    </tabContentItem>
    <tabContentItem>
      <label textWrap="true">This tab will list compelted tasks for tracking.</label>
    </tabContentItem>
  </tabs>
</page>

<script>
  import { Template } from 'svelte-native/components';

  let todos = [];
  let dones = [];
  const removeFromList = (list, item) => list.filter(t => t !== item);
  const addToList = (list, item) => [ item, ...list ];
  let textFieldValue = "";

  async function onItemTap(args) {
    let result = await action("What do you want to do with this task?", "Cancel", [
      "Mark completed",
      "Delete forever"
    ]);

    console.log(result); // Logs the selected option for debugging.
    let item = todos[args.index];
    switch (result) {
      case "Mark completed":
        dones = addToList(dones, item) // Places the tapped active task at the top of the completed tasks.
        todos = removeFromList(todos, item); // Removes the tapped active task.
        break;
      case "Delete forever":
        todos = removeFromList(todos, item); // Removes the tapped active task.
        break;
      case "Cancel" || undefined: // Dismiss the dialog
        break;
    }
  }

  function onButtonTap() {
    if (textFieldValue === '') return; // Prevents users from entering an empty string.
    console.log("New task added: " + textFieldValue + "."); // Logs the newly added task in the console for debugging.
    todos = [{ name: textFieldValue }, ...todos]; // Add tasks in the todos array. Newly added tasks are immediately shown on the screen.
    textFieldValue = ""; // Clears the text field so that users can start adding new tasks immediately.
  }
</script>
