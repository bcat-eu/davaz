## About
We want to digitalize the full Davatz, Davats, Taffattz, Davaz, etc. family tree using the [Gramps Project](https://gramps-project.org).

## Davaz Family tree
We use [Gramps XML format](https://gramps-project.org/wiki/index.php/Database_Formats): 
Export > Gramps XML (we have no media) > No compression (for clearer diffs) > davaz.gramps. 

## Collaboration Workflow

There is one quirk which has more to do with Gramps, but affects our workflow - importing a family tree into existing one merges it instead of replacing. So if I import the updated tree after merging external changes in github, it will create duplicates of all existing people and add new ones. To import actual XML from the repository one would need to start an empty family tree (Family Trees > Manage Family Trees > New). We could also use that as a feature if we ever had conflicts that we can't solve.

The XML itself seems to be about generating <person>, <family> and <event> nodes (we also have <places>, but there are very few and it uses similar logic). Each such node has few properties - `handle` (UUID-like hash which is used as an actuall identifier to connect different nodes to eachother), `change` (looks like an integer timestamp of the last change) and `id` - this one looks like a string of a letter identifying the node ("I" for person, "F" for family, "E" for event) and a sequential integer. I am not sure as to why it exists, but it will cause problems if we do changes in parallel. To fix conflicts on such IDs, the branch that needs to be merged would have to find the latest node of each type in master (they are generated top to bottom so the one with the last number would be the last one in the XML file), take its number, add 1 and adjust its own nodes so that they start with that number and go from there. 

Example for persons: Master has I0127 as ID of the last person and my branch has few entries that are below 127 so the IDs are duplicated. I'd have to change the ID of the first new person in my XML to I0128 and then go down the list and make them I0129, I0130 etc. Then do the same with events etc, that should suffice. As a side note - the more changes one has, the more work it is to adjust these IDs, so it's better to merge often.

I also checked what happens if we don't do that and could see no actual problems: I have created a duplicate ID (I0022) which was shared by 2 person nodes and imported the resulting XML, I could see this ID in the UI but other then that it didn't seem to affect anything. So it seems these IDs are mostly cosmetic and the steps to make them unique as described above are actually optional.

If we ever had a conflict on `handle` property, that would be tricky cause the nodes are linked using values of that property, but they look like UUIDs so I assume it conflicts are so unlikely that we can consider them impossible.

There is also `priv` property that appears on some person nodes and seems to always have a value of "1", I assume it's some boolean set in the UI which is of no consequence for our workflow.

## License
The License of the is Project is GPLv3.0, see the License file.
