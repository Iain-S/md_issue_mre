# Project name here
> Summary description here.


This file will become your README and also the index of your documentation.

## Install

`pip install your_project_name`

## How to use

Fill me in please! Don't forget code examples:

```python
1 + 1
```




    2



## DataFrames in GitHub Markdown

### The Issue

The default pandas output uses a `<style scoped>...</style>` element, which is neither
stripped nor formatted nicely by GitHub.

```python
import pandas as pd

d = {"col1": list(range(100)), "col2": list(range(100))}
df = pd.DataFrame(data=d)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>col1</th>
      <th>col2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>



### A Solution

Subclassing DataFrame, we can display the df without the `<style...` bit.

```python
import re

import pandas as pd


class GitHubMarkdownDataFrame(pd.DataFrame):
    """DataFrame that strips <style> tags when used in a Notebook."""

    def _repr_html_(self):
        """Override parent's method."""
        original = super()._repr_html_()

        # See https://stackoverflow.com/a/55148480/3324095
        stripped = re.sub(
            "<style scoped>.*</style>\n",  # replace the CSS...
            "",  # ...with an empty string
            original,
            flags=re.DOTALL,  # match across multiple \n lines
        )

        return stripped
```

```python
GitHubMarkdownDataFrame(data=df)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>col1</th>
      <th>col2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>95</td>
      <td>95</td>
    </tr>
    <tr>
      <th>96</th>
      <td>96</td>
      <td>96</td>
    </tr>
    <tr>
      <th>97</th>
      <td>97</td>
      <td>97</td>
    </tr>
    <tr>
      <th>98</th>
      <td>98</td>
      <td>98</td>
    </tr>
    <tr>
      <th>99</th>
      <td>99</td>
      <td>99</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 2 columns</p>
</div>



```python
# This doesn't work, as is
GitHubMarkdownDataFrame(data=df).head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>col1</th>
      <th>col2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>



### Not a good solution

```python
from pandas.io.formats.style import Styler

# from pandas.util._decorators import (
# Substitution,
# )


class MyOtherDF(pd.DataFrame):
    @property
    def style(self) -> Styler:
        """
        Returns a Styler object.
        Contains methods for building a styled HTML representation of the DataFrame.
        See Also
        --------
        io.formats.style.Styler : Helps style a DataFrame or Series according to the
            data with HTML and CSS.
        """
        # raise Exception()
        print("in style")

        class MyStyler(Styler):
            # @Substitution(buf=buf, encoding=encoding)
            def to_html(
                self,
                **kwargs,
            ):
                print("calling super.to_html")
                # kwargs["exclude_styles"] = True
                return super().to_html(**kwargs)

            def _repr_html_(self):
                print("in repr")
                return super()._repr_html_()

        return MyStyler(self)
```

```python
MyOtherDF(data=df)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>col1</th>
      <th>col2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>95</td>
      <td>95</td>
    </tr>
    <tr>
      <th>96</th>
      <td>96</td>
      <td>96</td>
    </tr>
    <tr>
      <th>97</th>
      <td>97</td>
      <td>97</td>
    </tr>
    <tr>
      <th>98</th>
      <td>98</td>
      <td>98</td>
    </tr>
    <tr>
      <th>99</th>
      <td>99</td>
      <td>99</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 2 columns</p>
</div>


