Blog posts
==========
for [https://itenium.be]

[Instructions on how to create a post and what is available](https://itenium.be/blog/productivity/create-blog-post/)

[Github pages supported plugins](https://pages.github.com/versions)

## New post

A new post will be published when: 

- The filename starts with a date
- The file is not in the `_drafts` folder
- The front matter date is not in the future
- The submodule of this project is updated to include the new post(s)
- The Jekyll project is pushed to Github

**Conventions:**  

- Place image(s) and other resources in the `assets/blog-images` folder of https://github.com/itenium-be/itenium-be.github.io  
- New tag? Assign it an icon in `_data/blog.yml`  
- New category? Update `blog.yml` to set the icon!
- `last_modified_at` frontmatter needs to be kept in sync manually because sorting on `(updates | last).date` turned out to not be very easy.
- A `autohotkey` code block makes a notice appear about DynaRun
- Posts with first tag "meta" are not included in the search results (`blog/search.json`)
