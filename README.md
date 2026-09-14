## About

We want to digitalize the full Davatz, Davats, Taffattz, Davaz, etc. family tree using the [Gramps Project](https://gramps-project.org).

## Davaz Family Tree

We use [Gramps XML format](https://gramps-project.org/wiki/index.php/Database_Formats):
Export > Gramps XML (we have no media) > No compression (for clearer diffs) > davaz.gramps.

## GEDCOM File

We use the GEDCOM file to generate PDFs, so we regenerate it after major tree changes.

The export logic is similar to XML, but we also exclude living people as well as people marked as private so that they do not appear in the resulting PDF.

## Collaboration Workflow

There is one quirk which has more to do with Gramps, but affects our workflow — importing a family tree into an existing one merges it instead of replacing. So if you import the updated tree after merging external changes on GitHub, it will create duplicates of all existing people and add new ones. To import actual XML from the repository, start an empty family tree (Family Trees > Manage Family Trees > New). We could also use that as a feature if we ever had conflicts that we can't solve.

The XML generates `<person>`, `<family>` and `<event>` nodes (we also have `<places>`, but there are very few and they use similar logic). Each node has a few properties:

- **`handle`** — UUID-like hash used as the actual identifier to connect different nodes to each other.
- **`change`** — Integer timestamp of the last change.
- **`id`** — A string with a letter identifying the node type ("I" for person, "F" for family, "E" for event) followed by a sequential integer.

### ID Collision During Merges

The sequential `id` values will cause problems if we make changes in parallel. To fix conflicts, the branch that needs to be merged would have to find the latest node of each type in master (they are generated top to bottom, so the last number is at the end of the XML file), take its number, add 1, and adjust its own nodes so that they start from there.

**Example for persons:** Master has `I0127` as the last person ID and my branch has entries below 127, so the IDs are duplicated. Change the first new person's ID to `I0128`, then continue down the list as `I0129`, `I0130`, etc. Do the same with events and families. The more changes one has, the more work it is to adjust these IDs, so it's better to merge often.

Note: ID collisions are mostly cosmetic. Duplicate IDs import fine and don't seem to affect anything in the UI. The steps above to make them unique are optional.

### Handle Collisions

If we ever had a conflict on the `handle` property, that would be tricky because nodes are linked using these values. However, they look like UUIDs, so conflicts are extremely unlikely.

### Privacy

The `priv` property appears on some person nodes with a value of "1". This boolean marks people as private so they are excluded from the GEDCOM export.

## Documents

The `docs/` folder holds publication-ready material generated from — or related to — the tree.

- **[Thomas-Davatz-DE-EN-PT.pdf](docs/Thomas-Davatz-DE-EN-PT.pdf)** — A trilingual portrait (German, English, Portuguese) of the emigrant **Thomas Davatz (1815–1888)**, the schoolteacher from Fideris in the Prättigau who emigrated to Brazil in 1855, ended up in debt bondage under the *parceria* system on the Ibicaba coffee plantation near Limeira (São Paulo), led the colonists' revolt of December 1856, and changed European emigration policy with his 1858 report published in Chur. Sérgio Buarque de Holanda's 1941 translation, *Memórias de um colono no Brasil*, made him a standard source in Brazilian historiography.

  **Note:** Thomas Davatz is *not* recorded in `davaz.gramps`. The file contains no reference to Brazil, Ibicaba or Fideris. His dates come from the historical literature, not from verified parish registers, and should be checked in the Grisons church and civil registers before he is entered into the tree.

- **thomas-davatz.html** — Source of the PDF. Rebuild with [WeasyPrint](https://weasyprint.org):

  ```
  weasyprint docs/thomas-davatz.html docs/Thomas-Davatz-DE-EN-PT.pdf
  ```

  The layout uses Source Serif Pro and Source Sans Pro; substitute fonts change the pagination.

  **This is the text-only edition.** A longer version adds a plate section with photographs from the family gathering in the Val Müstair on 13 September 2026. Those photographs show identifiable people and are deliberately kept out of this public repository; they are circulated privately instead.

## Claude Code

See [CLAUDE.md](CLAUDE.md) for AI-assisted development guidance.

## License

The license of this project is GPLv3.0, see the [LICENSE](LICENSE.md) file.
