### What's changed in v1.12.0

* chore: update readme (by @patrickleet)
* chore: document things (by @patrickleet)
* feat(deps): update unbounded-tech/action-vnext action to v1.3.0 (#18) (by @renovate[bot])

  ####  Release Notes

  ###### [](https://redirect.github.com/unbounded-tech/action-vnext/releases/tag/v1.3.0)

  [Compare Source](https://redirect.github.com/unbounded-tech/action-vnext/compare/v1.2.0...v1.3.0)

  ##### What's changed in v1.3.0

  - chore(deps): update docker/login-action action to v3.5.0 ([#&#8203;21](https://redirect.github.com/unbounded-tech/action-vnext/issues/21)) (by [@&#8203;renovate](https://redirect.github.com/renovate)\[bot])

    Co-authored-by: renovate\[bot] <29139614+renovate\[bot][@&#8203;users](https://redirect.github.com/users).noreply.github.com>
  - feat(deps): update dependency unbounded-tech/vnext to v1.6.0 ([#&#8203;22](https://redirect.github.com/unbounded-tech/action-vnext/issues/22)) (by [@&#8203;renovate](https://redirect.github.com/renovate)\[bot])

    #####  Release Notes

    ###### [](https://redirect.github.com/unbounded-tech/vnext/releases/tag/v1.6.0)

    [Compare Source](https://redirect.github.com/unbounded-tech/vnext/compare/v1.5.0...v1.6.0)

    ##### What's changed in v1.6.0

    - feat: changelog header scaling ([#​54](https://redirect.github.com/unbounded-tech/vnext/issues/54)) (by [@&#8203;​patrickleet](https://redirect.github.com/patrickleet))

      Scales headers from commit bodies when creating the changelog so they look nicer in relation to the release notes h3 title. H1, H2, and H3 are scaled down three sizes to H4, H5, and H6, respectively.

    ###### [](https://redirect.github.com/unbounded-tech/vnext/releases/tag/v1.5.0)

    [Compare Source](https://redirect.github.com/unbounded-tech/vnext/compare/v1.4.0...v1.5.0)

    ##### What's changed in v1.5.0

    - feat: generate-deploy-key command ([#​53](https://redirect.github.com/unbounded-tech/vnext/issues/53)) (by [@&#8203;​patrickleet](https://redirect.github.com/patrickleet))

      This adds a new subcommand to vnext that simplifies setting up deploy keys for GitHub repositories:

      - Automatically detects current repository owner and name when possible
      - Generates an [](https://redirect.github.com/unbounded-tech/vnext/commit/Ed25519) SSH key pair
      - Sets the private key as a GitHub repository secret
      - Adds the public key as a deploy key to the repository
      - Provides improved error handling and user prompts
      - Includes comprehensive documentation in README

      This feature is particularly useful for CI/CD workflows where the default GITHUB\_TOKEN
      cannot trigger other workflows, such as when using the shared workflow at
      unbounded-tech/workflow-vnext-tag.

    ###### [](https://redirect.github.com/unbounded-tech/vnext/releases/tag/v1.4.0)

    [Compare Source](https://redirect.github.com/unbounded-tech/vnext/compare/v1.3.1...v1.4.0)

    ##### What's changed in v1.4.0

    - chore(deps): update unbounded-tech/workflow-vnext-tag action to v1.11.0 ([#​51](https://redirect.github.com/unbounded-tech/vnext/issues/51)) (by [@&#8203;​renovate](https://redirect.github.com/renovate)\[bot])

      Co-authored-by: renovate\[bot] <29139614+renovate\[bot][@&#8203;​users](https://redirect.github.com/users).noreply.github.com>
    - feat: rm github flag, detection by remote is enough ([#​52](https://redirect.github.com/unbounded-tech/vnext/issues/52)) (by [@&#8203;​patrickleet](https://redirect.github.com/patrickleet))

      - feat: rm github flag, detection by remote is enough

      - chore: correct readme github flag info
