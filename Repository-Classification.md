# Repository-Classification

MDA repositories run the gamut from code under active development to stale code that is no longer useful. To document the status of repositories, use one of the five following tags.

---------------------

### Stable

![lifecycle](https://img.shields.io/badge/lifecycle-stable-green.svg)

This should be the standard tag for repositories that are not under active development. Stable code should have no anticipated breaking changes and have received some QA. To mark  a repository as stable:

- Paste `![lifecycle](https://img.shields.io/badge/lifecycle-stable-green.svg)` into the `readme` and commit changes
- Add 'stable' as a topic for the repository

---------------------

### Maturing

![lifecycle](https://img.shields.io/badge/lifecycle-maturing-blue.svg)

Maturing repositories are under active development. This tag should not be used long-term. It indicates that breaking changes are expected in the code. To be labeled 'Maturing', the existing code should have received some level of QA but is subject to change. To mark  a repository as maturing:

- Paste `![lifecycle](https://img.shields.io/badge/lifecycle-maturing-blue.svg)` into the `readme` and commit changes
- Add 'maturing' as a topic for the repository

---------------------

### Experimental

![lifecycle](https://img.shields.io/badge/lifecycle-experimental-orange.svg)

This tag should be used sparingly -- ideally we should have few experimental repos. Code in an experimental repo may not have received any QA. Nothing generated from an experimental repo should be used in a deliverable or as the foundation for other code without serious external review. To mark  a repository as experimental:

- Paste `![lifecycle](https://img.shields.io/badge/lifecycle-experimental-orange.svg)` into the `readme` and commit changes
- Add 'experimental' as a topic for the repository

---------------------

### Deprecated

![lifecycle](https://img.shields.io/badge/lifecycle-deprecated-red.svg)

We may wish to preserve legacy code in some repositories. These should be marked as deprecated and link to the current state of the art. To mark  a repository as deprecated:

- Paste `![lifecycle](https://img.shields.io/badge/lifecycle-deprecated-red.svg)` into the `readme` and commit changes
- Add 'deprecated' as a topic for the repository

---------------------

### Unsupported

![lifecycle](https://www.repostatus.org/badges/latest/unsupported.svg)

Some repositories contain code that is being used but should not receive additional development. This tag is intended to denote for ourselves and for anyone who comes after that if additional development on the tool or project associated with the repo is desired, there is a strong recommendation to rewrite the code entirely and not to treat the existing code as a useful or reliable foundation for future work. 

In essence, this tag indicates the presence of catastrophic technical debt. 

To mark  a repository as unsupported:

- Paste `![lifecycle](https://www.repostatus.org/badges/latest/unsupported.svg)` into the `readme` and commit changes
- Add 'unsupported' as a topic for the repository
