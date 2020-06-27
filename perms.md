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

### (3) Administrate
Entities with the Administrate permission level cannot be demoted, except by others of the same level.
Similarly, Administrate cannot be granted by Entities of a lower level.

This is useful to ensure that Entities with the Write permission level cannot do irreversible damage.