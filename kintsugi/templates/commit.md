# Kintsugi commit template

Commit the failing test first (if repo convention allows), then the
fix. The fix commit follows this shape:

```
fix(<scope>): <short description — what the user-observable behaviour now is>

Crack:
<one line: what was broken, and what the user-observable symptom was>

Gold:
<one or two lines: the change in behaviour. Not a diff summary —
what the code now does that it didn't before.>

Seam:
<one line: where the behaviour is now anchored. Usually the test path
+ name — that's the durable guard. A named helper or constant
introduced by the fix goes here too. Not the ticket number alone;
that rots.>
```

## Subject line

- Conventional prefix (`fix`, `fix(auth)`, `fix(billing)`, etc.) — but
  this is bug-specific. If the repo uses a different style (e.g.,
  `[BUG] ...`), match the repo.
- Describe the behaviour now, not the code change. "blog comment form
  preserves author name on validation error" > "add escape to author
  field".

## Example

```
fix(forms): preserve user-submitted author name on blog-comment validation error

Crack:
Author name field cleared on submit if email was invalid; user had to retype.

Gold:
Server now escapes and re-echoes `form.author` in the re-render path
(blog-comment-form.liquid). Form state survives validation round-trip.

Seam:
Named helper `blog_author_value` in blog-comment-form.liquid;
test: specs/blog_comment_form_spec.rb `preserves author on validation error`.
```

## Anti-patterns

- `fix: typo` — tells the next reader nothing.
- `fix: bug #4217` — the ticket will eventually be gone or rot; the
  commit stays.
- Listing the diff: "add validation to foo, change null to empty
  array in bar". The crack/gold/seam triad tells the story in terms
  of behaviour, not diff.
