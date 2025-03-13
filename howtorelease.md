# How to release ScopeSim

This guide also applies to all other packages that use a `CHANGELOG.md` file (e.g. spextra, pyckles). Those that don't (e.g. ScopeSim-Templates, skycalc_ipy, astar-utils) have a somewhat simpler process that will be added soon. The IRDB works totally different.

_In all steps, of course replace the placeholder "X.Y.Z" with your actual version number you intend to release!_

1. Go to GitHub page and click on "Releases" on the right.
2. Click on "Draft a new release".
3. Click on "Choose a tag" and then type the new version number (including "v") in the Combobox, then click on the suggested "Create new tag: vX.Y.Z on publish".
4. Click on "Generate release notes".
5. Copy the markdown that was generated, verify that the version numbers in the final line ("Full Changelog") are correct.
6. **IMPORTANT: DO NOT** publish the release yet! Discard by simply closing the tab.
7. Make sure you're on a "rc" version (if not, increment from alpha/beta via `poetry version prerelease --next-phase` (twice if on alpha). Also make sure you're on a new branch (e.g. "prepare-vX.Y.Z").
8. Open `CHANGELOG.md` in an editor of your choice.
9. Paste the previously copied release notes in and add "# Version X.Y.Z" plus the date on top and optionally an additional release message. Copy your release message if you added one. See the previous sections in the CHANGELOG file for how to format everything.
10. Commit the changes, push and create a PR. **IMPORTANT: BEFORE clicking on the green button, add the `no release notes` label!**. This will ensure the markdown link checker is skipped and doesn't trip on the (yet non-existing) new tag link.
11. Make sure the changes are what you'd expect and wait for the tests to go green.
12. Merge the PR. Don't merge any additional PRs now until the release process is done!
13. On the GitHub page, go to the "Actions" tab.
14. Click on the "Bump package version" workflow on the left.
15. Click on "Run workflow" and select "prerelease --next-phase" for the version bump level. Keep the target branch as "main". **IMPORTANT: MAKE ABSOLUTELY SURE you select "prerelease --next-phase" and nothing else!** Any mistake here can only be corrected by application of nuclear options. If unsure, you can run the workflow with "Only output new version number" selected, make sure it's correct and then run it again with the same settings.
16. After a few seconds, the workflow should appear with a yellow "in progress" symbol. Clicking on it will reveal the message "Bumping version from X.Y.Zrc0 to X.Y.Z". Make sure the final version number is the one you intend to release!
17. Go back to the GitHub page, you should now see your new version number in the teal coloured `dev version` badge (may take a minute to update).
18. Repeat steps 1.-4.
19. Change the title to "Version X.Y.Z".
20. If you added a release message to the `CHANGELOG.md` file, copy it now on top of the auto-generated release notes. The text should now be exactly the same as in the CHANGELOG, except the title and date (which get added as separate fields).
21. Finally, without changing any of the other settings, click the green "Publish release" button.
21. Go back to the "Actions" tab and look for the one named after your new release, which should be running already.
22. If all three steps go green and mention the correct new version number: Congratulations, you have successfully published the new release!!

The last workflow step will automatically increment the dev version number to the next patch level. You're now all done and can continue working as usual. Perhaps pull `main` in your clone. If you take a look at the git history, the commit on main named "Bumping version from X.Y.Zrc0 to X.Y.Z" should be the one tagged with the new `vX.Y.Z` tag.
