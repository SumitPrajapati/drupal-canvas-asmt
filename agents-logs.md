# Agent guidance for this Drupal site

This codebase is a Composer-managed Drupal site.

## Detect the command runner first

Do not assume DDEV is installed. Before running anything, work out how this codebase invokes Composer
and Drush, from the project root:

1. **DDEV (preferred)** — a `.ddev/` directory exists *and* `ddev` is on `PATH` (`command -v ddev`).
   Use `ddev composer` and `ddev drush`, as described below.
2. **Local PHP + Composer** — no usable `ddev`, but `composer` is on `PATH` (`command -v composer`).
   Run `composer install` if `vendor/` doesn't exist yet. Use plain `composer`, and check
   whether `vendor/bin/drush` exists (some packages of this project ship without Drush), if it does, use that (not a global `drush`); if it doesn't, skip the Drush workflow below.

State which one you picked before running commands. If neither applies, stop and report it rather
than guessing at a command that will fail. In the workflow sections below, substitute the Composer
and Drush commands for the option you resolved.

## Local environment (DDEV)

Run commands from the project root:

- Start or restart the local environment with `ddev start`, `ddev restart`, and `ddev stop`.
- Install PHP dependencies with `ddev composer install`.
- Open the site with `ddev launch`.
- Run Drush commands with `ddev drush <command>` such as `status`, `user:login`,  `cache:rebuild`, and `updatedb`.

## Agents used

Multiple AI agents used like Codex, Claude Agent:

# Claude is returning the better response as compare others agents.

# Prompts - 

This prompt use in ChatGPT codex - Use this design image, identify reusable components, and define component specifications. Review and refine the specifications, making architectural decisions about component boundaries, reusability, and editor flexibility. The specification should be stored in component-specs.md as part of the submission.

Claude - Please use home-page.png image to generate a Drupal CMS canvas component as mention below section in home-page.png. Attaching image for Audience Resources Section – four reusable audience cards for Makers, Buyers, Distributors, and Service Providers. This should be editable by autheticated users.

I am not using any javascript framework like react, nextjs please provide code without any js framework

Claude - Please use home-page.png image to generate a Drupal CMS canvas component as mention event hero section in home-page.png. This should be editable by autheticated users. i am not using any javascript framework like react, nextjs please provide code without any js framework.


