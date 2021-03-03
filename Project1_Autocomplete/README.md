# Project 1 - Autocomplete

The objective of this project is to write a fast implementation of the autocomplete functionality.

The autocomplete task is to quickly retrieve the top terms that match a query string. For example, if the query string is "Eng", the matches might be "English", "Engineering", and "EngSci".

Files containing a large number of terms, along with the associated importance weightings are given.

To accomplish this task, the following items will have to be completed:

* Read in the entire file of terms, and sort the terms in lexicographic ordering. This sorted list will be reused for multiple queries.
* Use binary search to find the location of the first term in lexicographic ordering that matches the query string, as well as the last term in lexicographic ordering that matches the query string.
* Extract the terms that match the query string, and sort them by weight.
* Extract the top matching terms by weight.

The following struct is utilized for this project:
```
struct term{
  char term[200]; // assume terms are not longer than 200
  double weight;
};
```

## Part 1

```
void read_in_terms(struct term **terms, int *pnterms, char *filename);
```

The function takes in a pointer to a pointer to `struct term`, a pointer to an `int`, and the name of a file that is formatted like 'cities.txt' and 'wikitionary.txt'.

The function allocates memory for all the terms in the file and stores a pointer to the block in `*terms`. The function stores the number of terms in `*pnterms`. The function reads in all the terms from filename, and places them in the block pointed to by `*terms`. The function also sorts the terms in lexicographic order.