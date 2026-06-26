from pathlib import Path
from datetime import datetime
import re


class SmartFileRenamer:

    def __init__(self, folder):
        self.folder = Path(folder)

    def clean_name(self, name):
        name = name.lower()

        name = re.sub(r"[^a-zA-Z0-9]", "_", name)

        name = re.sub(r"_+", "_", name)

        return name.strip("_")

    def rename_files(self):
        renamed = 0

        for index, file in enumerate(self.folder.iterdir(), start=1):

            if not file.is_file():
                continue

            date = datetime.fromtimestamp(
                file.stat().st_mtime
            ).strftime("%Y%m%d")

            clean = self.clean_name(file.stem)

            new_name = (
                f"{date}_{index:03d}_{clean}"
                f"{file.suffix.lower()}"
            )

            target = file.parent / new_name

            if target.exists():
                continue

            file.rename(target)

            renamed += 1

            print(
                f"{file.name} -> {new_name}"
            )

        print(f"\nRenamed {renamed} files")


def main():

    print("=" * 50)
    print("Smart File Renamer")
    print("=" * 50)

    folder = input(
        "Folder path: "
    ).strip()

    renamer = SmartFileRenamer(folder)

    renamer.rename_files()


if __name__ == "__main__":
    main()
