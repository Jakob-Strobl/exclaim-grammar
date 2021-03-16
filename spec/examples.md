# Examples

Working ideas of the language.

---

## Example A

Let's say we have the following data:  

| song | title |
| ---- | ----- |
| A    | Once  |
| B    | Twice |

If we wanted to have a header that lists the titles of every song, we would write.

### Example A.1

```md
{{ let! songs = site.songs }}
### Titles
{{ render! song : songs }}
- {{ write! song.title }}
{{!}}
```

### Example A.2

```md
### Titles
{{ render! song : site.songs }}
- {{ write! song.title }}
{{!}}
```

### Example A Result

A.1 and A.2 are equivalent.  

```md
### Titles
- Once
- Twice
```

---

## Example B

Get the top post of all time and also print the top 3 most liked posts.

Following data:

| post | title | date | likes |
| ---- | ----- | ---- | ----  |
| A    | Day 1 | 01/01/2021 | 10 |
| B    | Day 2 | 02/01/2021 | 15 |
| C    | Day 3 | 03/01/2021 | 13 |
| D    | Day 4 | 04/01/2021 | 7  |

### Example B.1

```md
{{ let! postsByLikes = site.posts | sort("likes") | reverse }}
# All-Time Top Post: {{ write! topPost.title }}

# Top 3: 
{{ render! (post, index) : postsByLikes | take(3) | enumerate }}
{{ write! index }}. {{ write! post.title }}
{{!}}
```

### Example B Result

```md
All-Time Top Post: Day 2

Top 3:
1. Day 2
2. Day 3
3. Day 1
```
