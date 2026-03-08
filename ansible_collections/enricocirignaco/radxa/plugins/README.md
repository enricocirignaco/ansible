# Plugins - enricocirignaco.radxa

This directory contains custom Ansible plugins for the `enricocirignaco.radxa` collection.

## Layout

Place plugins in the corresponding subdirectory by type, for example:

- `plugins/modules/` for custom modules
- `plugins/module_utils/` for shared module helpers
- `plugins/filter/` for Jinja2 filters
- `plugins/lookup/` for lookup plugins

## Notes

- Keep plugin names stable once published to avoid breaking playbooks.
- Document each plugin with `DOCUMENTATION`, `EXAMPLES`, and `RETURN` blocks.
- Add unit/integration tests when introducing new plugin behavior.

Reference: [Ansible plugin types and development docs](https://docs.ansible.com/ansible-core/2.20/plugins/plugins.html)
