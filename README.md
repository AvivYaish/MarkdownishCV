# MarkdownishCV

- Uses [Beuaitful Soup](https://pypi.org/project/beautifulsoup4/) to easily tailor your CV by adding/removing sections
- Uses [weasyprint](https://weasyprint.org/) to create PDF output
- Can insert page breaks into PDF output using `<div class="page-break"></div>`
- Sample files:
  - `cv.pdf`: PDF version of the sample
  - `code.py`: cleans up code and creates PDF output
  - `style.css`: provides styling for PDF outputs when using weasyprint, defines the page break tag
  - `main.md`: sample input file
  - `clean.md`: cleaned sample

## cv.pdf

![image](https://github.com/user-attachments/assets/4a12fef7-163f-47be-9488-8debd3704942)

## code.py

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

# Convert the .md to .pdf
print(subprocess.Popen(f'pandoc {clean_file} --css style.css --pdf-engine=weasyprint --metadata title="" --from markdown+startnum -o cv.pdf', cwd=Path('.').absolute(), shell=True, stdout=subprocess.PIPE, stderr=subprocess.STDOUT).stdout.read())
```

## style.css

```css
@media print {
    @page {
        size: A4 portrait;

        @bottom-center {
            content: counter(page) " of " counter(pages);
        }
    }
}

html {
    font-size: 8pt;
    font-family: 'Palatino';
}

body {
    margin: 0;
    padding: 0;
}

a {
    text-decoration: none;
    color: rgb(44, 44, 128);
}

h1 {
    margin-top: 0;
    margin-bottom: 0;
}

h2 {
    font-size: 10pt;
    margin-top: -2pt;
    border-bottom: 0.75pt solid;
    border-bottom-color: black;
}

h4 {
    margin-top: 4pt;
    margin-bottom: -5pt;
}

ul,
ol {
    padding-left: 1rem;
}

.page-break {
    page-break-after: always;
}
```

## main.md

```markdown
<style>
  .gold {
    color: rgb(218, 165, 32);
  }

  .year {
    float: right;
  }

  .ongoing {
    opacity: 0.0;
  }
</style>

<center>
  <h1>Your Name</h1>

  <a href="mailto:your@email.com">✉ your@email.com</a>
  |
  <a href="https://x.com/your_x_twitter">𝕏 your_x_twitter</a>
  |
  <a href="https://yourdomain.com">👤 yourdomain.com</a>
</center>

<p class="paragraph" style="text-align: justify;">

## Paragraph

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
</p>

<span class="education">

## Education

- <span class="year">2024 - <span class="ongoing">2024</span></span>
  **Degree 1.** Institute. Host: *Your Host*  
  Some description.  
  More text. <!-- Comment for yourself -->
- <span class="year">2023 - 2024</span>
  **Degree 2.** Institute. Host: *Your Host*  
  <span class="gold">🏆</span> Award
- <span class="year">2023 - 2023</span>
  **Degree 3.** Institute. Host: *Your Host*  
  - Sub-list.
  - Another item.

</span>

<span class="papers">

## Papers

1. <span class="year">**<u>CONF'24</u>**</span>
  **[Paper 1](https://www.link.com)**  
  Authors: <u>Your Name</u>, Friend's Name  
  In: *Proceedings* <span class="details">  
  <span class="gold">🏆</span> Award 1  
  <span class="gold">🏆</span> Award 2</span>
2. <span class="year">**<u>CONF'24</u>**</span>
  **[Paper 2](https://www.link.com)**  
  Authors: <u>Your Name</u>, Friend's Name  
  In: *Proceedings*

</span>

<div class="page-break"></div>

<span class="skills">

## Skills

- Skill 1
  - Sub-skill 1
  - Sub-skill 2
- Skill 2
- Skill 3

</span>
```

## clean.md

```markdown
<style>
  .gold {
    color: rgb(218, 165, 32);
  }

  .year {
    float: right;
  }

  .ongoing {
    opacity: 0.0;
  }
</style>
<center>
  <h1>Your Name</h1>
  <a href="mailto:your@email.com">✉ your@email.com</a>
  |
  <a href="https://x.com/your_x_twitter">𝕏 your_x_twitter</a>
  |
  <a href="https://yourdomain.com">👤 yourdomain.com</a>
</center>
<p class="paragraph" style="text-align: justify;">

## Paragraph

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
</p>
<span class="education">

## Education

- <span class="year">2024 - <span class="ongoing">2024</span></span>
  **Degree 1.** Institute. Host: *Your Host*  
  Some description.  
  More text. 
- <span class="year">2023 - 2024</span>
  **Degree 2.** Institute. Host: *Your Host*  
  <span class="gold">🏆</span> Award
- <span class="year">2023 - 2023</span>
  **Degree 3.** Institute. Host: *Your Host*  
  - Sub-list.
  - Another item.

</span>
<span class="papers">

## Papers

1. <span class="year">**<u>CONF'24</u>**</span>
  **[Paper 1](https://www.link.com)**  
  Authors: <u>Your Name</u>, Friend's Name  
  In: *Proceedings* <span class="details">  
  <span class="gold">🏆</span> Award 1  
  <span class="gold">🏆</span> Award 2</span>
2. <span class="year">**<u>CONF'24</u>**</span>
  **[Paper 2](https://www.link.com)**  
  Authors: <u>Your Name</u>, Friend's Name  
  In: *Proceedings*

</span>
<div class="page-break"></div>
<span class="skills">

## Skills

- Skill 1
  - Sub-skill 1
  - Sub-skill 2
- Skill 2
- Skill 3

</span>
```
