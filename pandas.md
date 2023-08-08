# Pandas

```
# Below are some quick examples

# Using Dataframe.apply() to apply function add column
def add_3(x):
   return x+3
df2 = df.apply(add_3)

# Using apply function single column
def add_4(x):
   return x+4
df["B"] = df["B"].apply(add_4)

# Apply to multiple columns
df[['A','B']] = df[['A','B']].apply(add_3)

# Apply a lambda function to each column
df2 = df.apply(lambda x : x + 10)

# Using Dataframe.apply() and lambda function
df["A"] = df["A"].apply(lambda x: x-2)

# Using Dataframe.apply() & [] operator
df['A'] = df['A'].apply(np.square)

# Using numpy.square() and [] operator
df['A'] = np.square(df['A'])

# Apply function NumPy.square() to square the values of two rows
#'A'and'B
df2 = df.apply(lambda x: np.square(x) if x.name in ['A','B'] else x)

# Apply function single column using transform()
def add_2(x):
    return x+2
df = df.transform(add_2)

# Using DataFrame.map() to Single Column
df['A'] = df['A'].map(lambda A: A/2.)

# Using DataFrame.assign() and Lambda
df2 = df.assign(B=lambda df: df.B/2)
```
