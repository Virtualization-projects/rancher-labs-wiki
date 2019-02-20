# TEMP PAGE - Example ideal PR template

> Screenshots are very helpful especially for any UI-related issues. On a Mac you can easily create a screenshot in a few seconds via:

> Screenshot snippet to clipboard: CMD + CTRL + Shift + 4 and paste in the issue where you want the screenshot snippet to be.

> (Optional) Screenshot snippet to desktop: CMD + Shift + 4


## Proposed Changes
- Adds support for Cloud Keys
- Modifies Node Template, the modal first asks for your cloud key (RKE)
![](https://i.ibb.co/2ZzL6C0/Screen-Shot-2019-02-20-at-10-18-29-AM.png)
- Adds a page under the user menu to manage Cloud Keys

## Types of Changes
New Feature

## Linked Issues
https://github.com/rancher/rancher/issues/18037

https://github.com/rancher/rancher/issues/18078

https://github.com/rancher/rancher/issues/18081

## How To Test
> If this adds or modifies functionality and the issue(s) do not clearly state how to test please describe below.
- Notice the new prompt for a cloud key when creating a node template. Simply follow the flow to test this area. 

- Ensure you can create/edit/clone/delete cloud keys from the Cloud Keys page under the user nav menu. Ensure node templates are still working correctly.

## Further Comments
Add any other additional comments here. Gotchas / things to look out for / etc.