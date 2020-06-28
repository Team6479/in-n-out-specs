# Permissions
This document describes available permissions and their representation.

## Levels
Each permission level also has the privileges of those beneath it. The following are listed from least to greatest.

### (0) Exist
Entities with the Exist permission level may be referenced as Characters but may not Act or Inquire.

### (1) Read
Entities with the Read permission level may read all available data.

### (2) Write
Entities with the Write permission level may create new Moments. However, they cannot overwrite existing Moments.

Entities with the Write permission level may not set the public key of another Entity of the same or higher permission level.

### (3) Administrate
Entities with the Administrate permission level cannot be demoted, except by others of the same level.
Similarly, Administrate cannot be granted by Entities of a lower level.

Additionally, the public key of an Administrator cannot be changed by Entities of lower permission levels.
However, they may set the public key of any Entity.

This is useful to ensure that Entities with the Write permission level cannot do irreversible damage.