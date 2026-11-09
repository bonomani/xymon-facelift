# xymon-facelift

Optional skins, page layouts and tools for the
[Xymon](https://github.com/xymon-monitoring/xymon) web interface.

It is a facelift, not a redesign: it changes how the pages look, not what
they show or how they work. A redesign would need an analysis of what users
need first, and is out of scope here.

## Rules

- **Xymon's default stays as it is.** Without any setting from this project, the
  pages Xymon generates are byte for byte what they are today, keep their META
  refresh, and load no extra files.
- **Everything here is opt-in.** An administrator installs it and switches it on,
  per installation, and can switch it off again.
- **Xymon itself only gets small changes that are off by default**, made in the
  Xymon repository through its own pull requests: the page refresh as a setting
  whose default is today's META line, opt-in icon classes, and text icons.
- **The rules are enforced by tests**, so a change that breaks one fails at once.

## Planned contents

| Part | What it does |
| --- | --- |
| skins | Icon sets beside the classic GIFs: accessible and vector (SVG), each with a still variant |
| layouts | Page layouts (modern, minimal), loaded through `XYMONBODYFOOTER`, so no Xymon template changes |
| lookandfeel | A page that previews a layout and an icon set on a real Xymon page, and shows the commands to keep it |
| xymonsettings.sh | A script that shows and changes `xymonserver.cfg` settings, and switches skins and layouts |

## Contributing

Discussion happens in the issues of this repository. Changes come as pull
requests; see [CONTRIBUTING.md](CONTRIBUTING.md).

## Licence

GNU General Public License version 2, the same as Xymon; see [LICENSE](LICENSE).
