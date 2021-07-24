# Changelog
This changelog documents material changes to the Flow Results specification. Versions of the specification adhere to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.1.0] - 2021-07-30 (proposed)

### Added

- Add optional `semantic_labels` on Question definitions. This corresponds with semantic_label from the Flow Specification, providing a place for organizations to specify a coding system or taxonomy over Flow Results. (#37)
- Add optional `is_personal_information` field on Question definitions. This provides a standard way of indicating fields that contain or might contain personal identifying information in their responses.
- Add optional `set_contact_property` field on Question definitions. This provides a standard way for Flow Results to convey to systems that maintain a contact database that the response within a Question should set a contact property. (#41)
- Add optional `set_group_membership` field on Question definitions. This provides a standard way for Flow Results to convey to systems that maintain a contact database that the response within a Question should add or remove a contact from a group. (#41)

### Changed

- Clarify that Row IDs should be compared as strings for uniqueness (#38)

## [1.0.0-rc.1] - 2017-09-30

- Complete and stable release candidate

