# ui-ux-pro-max

[中文](README_CN.md)

UI/UX design guidance with bundled searchable design data and portable Python search tools.

## Usage

Install and enable this plugin in ZCode, then use its `ui-ux-pro-max` skill. Slash-command spelling may be namespaced by the client.

Invoke `ui-ux-pro-max` or ask for UI/UX design guidance. Python 3 is needed for database searches; the written guide also works without Python. Resolve `scripts/search.py` relative to this installed skill, then run `python3 <skill-directory>/scripts/search.py "SaaS dashboard" --design-system`. All CSV data and Python tools are included; no API key or Python package installation is required.

## Source and license

Derived from [nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill), MIT. The bundled edition uses the existing local skill snapshot, adapted for ZCode; it is not a pristine checkout of the current upstream branch. The original SKILL.md SHA-256 before adaptation is `d459d0509f44b1aefaad2e7f758dc9c1a3ea4538adc5b1b519e44150c7e9ee9e`. The [upstream license reference](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill/blob/d14efff8ed1faa4187f004f79926f428c8074ec7/LICENSE) records the version used to retrieve the included [LICENSE](LICENSE). Original copyright notices are retained.
