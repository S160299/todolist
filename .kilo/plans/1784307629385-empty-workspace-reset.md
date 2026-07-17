# Empty Workspace Reset

## Goal

Reset the current Create React App todo repository into an empty Git-backed workspace for future, unrelated app-generation requests. Do not create a replacement app or choose a technology stack during this reset.

## Confirmed Boundaries

- Preserve `.git/` unchanged, including repository history, refs, remotes, and the current branch.
- Delete every other file and directory at the repository root, including hidden files and this `.kilo/` plan directory.
- The known tracked project content to remove includes `.gitignore`, `README.md`, `package.json`, `package-lock.json`, `public/`, and `src/`.
- Do not install dependencies, generate a scaffold, commit changes, push changes, or modify remote state.

## Implementation

1. Recheck `git status --short --branch` immediately before deletion so concurrent changes are visible. The user has explicitly chosen deletion of all non-`.git` content, so any newly created non-`.git` workspace content is also within the approved deletion boundary.
2. From the verified repository root, enumerate root entries including hidden entries and remove all of them except `.git/`. Use a deletion method that cannot descend into or match `.git/` accidentally; do not use `git reset`, `git clean`, or checkout-based restoration commands.
3. Verify the filesystem root contains only `.git/`.
4. Run `git status --short --branch` and confirm Git reports deletion of all formerly tracked project files while the repository and current branch remain valid.
5. Report that the workspace is empty and that deletions are uncommitted. Do not create a placeholder file merely to keep the directory populated.

## Risks And Safeguards

- This is intentionally destructive for all content outside `.git/`; Git-tracked files remain recoverable from history until a later commit/history rewrite, while untracked files will not be recoverable through Git.
- The critical failure mode is deleting `.git/`. Prevent it through repository-root verification, explicit exclusion, and post-delete Git status validation.
- Because the saved plan is outside `.git/`, it must be read before execution and deleted with the rest of `.kilo/` to satisfy the empty-workspace requirement.

## Validation

- Root directory listing contains exactly `.git/`.
- `git status --short --branch` succeeds on the existing branch.
- Git reports removals for all previously tracked application files.
- No app scaffold, package metadata, dependencies, documentation, configuration, or plan files remain outside `.git/`.
