### Project Structure
The structure of this project is the current standard of modern [TYPO3](https://github.com/TYPO3/TYPO3.CMS) projects 
based on composer. The theme and individual settings for a website are stored in a local composer package 
(called "site package" in the world of TYPO3). Individual extensions, which should not be publicly available, are also 
installed as local composer packages.

### Local packages
Using local packages is based on local repositories, which is supported by **composer** 
(https://getcomposer.org/doc/05-repositories.md#path). A locally required package does not contain a version and is 
required in root `composer.json` by using `composer req dahaupt/local-package:@dev` 
(https://stackoverflow.com/a/39802804/7037467).

### Current issues with Renovate
In case a local package declares another dependency (e.g. `firebase/php-jwt` within `dahaupt/local-package`), we have 
to execute `composer update dahaupt/local-package` in the project root in order to install the new sub-dependency 
`firebase/php-jwt`. If this new dependency now receives an update, currently only the version string in 
`packages/local-package/composer.json` is raised. In order for the project to really _perform_ the update, the command 
`composer update dahaupt/local-package -w` or `composer update firebase/php-jwt dahaupt/local-package` must be executed 
in project root.

### Conclusion:
Renovate would therefore need to detect that the dependency to be updated is in a local composer package, which would 
then also require the local composer package in the root `composer.json` to be updated.

#### PRs:
Both PRs basically are missing this update of the local package:

PR #2 - Expected **composer** command to update root `composer.lock`:
```
   composer update \
      typo3/cms-backend typo3/cms-core typo3/cms-extbase typo3/cms-extensionmanager \
      typo3/cms-filelist typo3/cms-fluid typo3/cms-frontend typo3/cms-install typo3/cms-recordlist \
      dahaupt/local-package \
      --with-dependencies --ignore-platform-reqs --no-ansi --no-interaction --no-scripts --no-autoloader
```

PR #3 - Expected **composer** command to update root `composer.lock`:
```
   composer update \
      dahaupt/local-package \
      firebase/php-jwt \
      --with-dependencies --ignore-platform-reqs --no-ansi --no-interaction --no-scripts --no-autoloader
```

**Final question:** Is there any existing mechanism to create this kind of logic?