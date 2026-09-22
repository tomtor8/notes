# Google translate on the command line

## Installation

```bash
sudo pacman -S translate-shell
```

## Usage

### Dictionary mode

```bash
trans -d es:en cerveza
```

You can use aliases e.g. `alias dic="trans -d es:en"` and then find a word `dic cerveza`

### Shell mode

Enter shell mode:

```bash
trans -shell es:en
```

Press `q` to quit the shell session.
