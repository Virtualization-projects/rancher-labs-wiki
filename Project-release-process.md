This article documents the standard release process for projects that make up Rancher. Once you release a project, you'll need to reference the new release in another project. Where it is referenced can vary project-to-project.

To release one of Rancher projects, do the following:

1. Clone a fresh copy of the project
 * Example: `git clone git@github.com:rancherio/python-agent.git`
2. Tag the release:
  * Example: `git tag v0.16.0`
3. Build the project's artifacts. If you have [build-tools/bin](https://github.com/rancherio/build-tools) on your PATH, it's a one-linerL
  * Example: `release`
4. Push the tag to github:
  * Example: `git push origin v0.16.0`
5. Go to the project's Release page in GitHub.
  * Example: https://github.com/rancherio/python-agent/releases
6. Edit the newly created release and upload the artifact (typically a tar.gz) to the release
7. Update Rancher's reference to the newly created release.
  * Python-agent example: [cattle/resources/content/cattle-global.properties](https://github.com/rancherio/cattle/blob/master/resources/content/cattle-global.properties#L11)
  * Go-machine-service example: [rancher/server/Dockerfile](https://github.com/rancherio/rancher/blob/master/server/Dockerfile#L33)

