# Glossary

| Term | Meaning |
| --- | --- |
| **ADR** | Architecture Decision Record. A short document recording one significant technical decision, its context and its consequences. See [decisions](architecture/decisions/README.md). |
| **Ancestor / descendant query** | A query that walks parent–child edges upward (ancestors) or downward (descendants) from a person. Implemented with recursive SQL. |
| **Common ancestor** | A person who is an ancestor of two given people. The nearest common ancestors determine how two people are related (e.g. second cousins share great-grandparents). |
| **Cape Verde / Cabo Verde** | The same country. Docs use the English "Cape Verde" in prose; place data uses the official name "Cabo Verde". |
| **Diaspora** | Cape Verdeans and their descendants living outside Cape Verde, with large communities in the United States (notably New England), Portugal, the Netherlands, France and elsewhere. |
| **Edge** | A relationship between two people stored as its own row: `parent_child` or `partnership`. |
| **Event** | A dated fact about a person or partnership: birth, baptism, marriage, death, emigration, residence, etc. |
| **Fuzzy date** | A date that may be uncertain or partial ("c. 1890", "before 1902", "between 1880 and 1885", "March 1911"). Stored as the original text plus a sortable range. See [data model](architecture/data-model.md#fuzzy-dates). |
| **GEDCOM** | GEnealogical Data COMmunication: the standard file format for exchanging family-tree data between genealogy programs. Versions 5.5.1 and 7.0 are in common use. |
| **Kriolu** | Cape Verdean Creole (Kabuverdianu), the everyday language of Cape Verde. |
| **Link visitor** | Someone viewing a tree through a share link, without signing in. Sees the protected view. |
| **Living person** | A person without a recorded death who could plausibly still be alive. A living person can never be marked public. |
| **Owner, editor, viewer** | The roles a signed-in user can hold in a tree. See [what each role sees](security/overview.md#what-each-role-sees). |
| **MADR** | Markdown Architectural Decision Records: the ADR template used by this project. |
| **Node** | A person in the family graph. |
| **Nome de casa** | A "house name" or nickname used in Cape Verde, often how someone is actually known locally, distinct from their legal name. Stored as `nickname`. |
| **Pedigree collapse** | When the same ancestor appears more than once in someone's family tree, usually because of marriages between relatives. Common in small island communities; the reason the family graph cannot be modelled as a simple tree. |
| **Place hierarchy** | Places nested inside larger places: a village inside an island inside a country (Nova Sintra → Brava → Cabo Verde). |
| **Protected view** | The default way people appear to viewers and link visitors: names, relationships, years and countries only. Enough to recognise someone, not enough to steal their identity. See [ADR-0009](architecture/decisions/0009-per-tree-roles-and-protected-view.md). |
| **Public (person)** | A deceased person an owner has opened up, so everyone who can see the tree sees their full details. |
| **Share link** | A revocable, optionally expiring link that lets anyone view a whole tree in the protected view. |
| **Tree** | The unit of access. Every person belongs to one tree; users are members of trees with a role. |
| **Repository layer** | The single backend module that holds all graph-traversal queries, so the rest of the app never writes them directly. See [ADR-0003](architecture/decisions/0003-postgresql-as-primary-database.md). |
