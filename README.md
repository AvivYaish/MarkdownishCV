# MarkdownishCV

![image](https://github.com/user-attachments/assets/4a12fef7-163f-47be-9488-8debd3704942)

A simple CV, mostly in Markdown, with some HTML and CSS.
- Uses [Beuaitful Soup](https://pypi.org/project/beautifulsoup4/) to easily tailor your CV by adding/removing sections
- Can insert page breaks into PDF output using `<div class="page-break"></div>`

```python
import re
import subprocess
from pathlib import Path
from bs4 import BeautifulSoup, Comment

# Wrap double space in <pre>, otherwise they will be removed
soup = BeautifulSoup(Path('./main.md').read_text().replace('  ', "<pre>  </pre>"))

# Remove HTML comments enclosed in <!-- -->
for comment in soup.find_all(string=lambda text:isinstance(text, Comment)):
    comment.extract()

# Remove certain sections if you want to, according to their class
for attr in [
    # 'paragraph',
    # 'education',
    # 'papers',
    'skills',
]:
    for item in soup.find_all(attrs={'class': attr}):
        item.extract()

# Remove unnecessary HTML code
output = str(soup)
for attr in ['<html>', '</html>', '<head>', '</head>', '<body>', '</body>', '<pre>', '</pre>']:
    output = output.replace(attr, '')

# Create a clean output .md file
clean_file = Path('./clean.md')
clean_file.write_text(output, encoding=None, errors=None)

# Convert the .md to .pdf using [weasyprint](https://weasyprint.org/)
print(subprocess.Popen(f'pandoc {clean_file} --css style.css --pdf-engine=weasyprint --metadata title="" --from markdown+startnum -o cv.pdf', cwd=Path('.').absolute(), shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT).stdout.read())
```
