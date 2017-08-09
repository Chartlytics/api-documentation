# Chartlytics API

# Headers

For all api end points other than the `signin` request, you will need to include a authorization header for the `JWT` token,
and a `x-timezone` header.

# Schemas

## `<Performer>`
```
  org: <string>
  name: <string>
  age: <optional number>
  gender: <optional string>
  grade: <optional string>
  user: <optional string>   // Link to a <User> for performers of `type` 'user'. You can ignore this.
  creator: <string>
  archived: <optional string>
  archivedBy: <optional string>
  type: <Enum('learner', 'user')> // Ignore performers of type user. User performers are used to track users activity.
```
## `<Authorization>`

```
  _id: <string>
  org: <string>
  performer: <string>
  user: <string
  roles: <[string]>
  active: <boolean>
  assigner: <string>
```

## `<User>`

```
  email: <optional string>
  username: <optional string>
  isGodmin: <boolean>
  activeOrg: <optional string>
  first_name: <optional string>
  last_name: <optional string>
  hasCaseSensitiveUsername: <boolean>
  isBetaTester: <boolean>
  gender: <optional boolean>,
  hasUpdatedPassword: <optional boolean>
  forceSetPasswordOnFirstLogin: <optional boolean>
  login_attempts: <number>
  lock_until: <optional number>
  confirmed: <boolean>
  lastLogin: <optional string>
```


## `<Chart>`

```
  globalType: <optional string>
  name: <optional string>
  org: <string>
  group: <optional string>
  performer: <optional string>
  pinpoint: <string>
  sortId: <number> // deprecated
  metadata: {
    recordFloor: <optional number>
    recordCeiling: <optional number>
    enableRecordCeiling: <optional Bboolean>
    correctAimMin: <optional number>
    correctAimMax: <optional number>
    incorrectAimMin: <optional number>
    incorrectAimMax: <optional number>
    durationAimMin: <optional number>
    durationAimMax: <optional number>
    latencyAimMin: <optional number>
    latencyAimMax: <optional number>
    startDate: <optional string>
    aimDate: <optional string>
    daysToAim: <optional number>
    aggregateType: <optional string>
    conditions: <optional [string]>
    totalTrials: <optional number>
    trialTime: <optional number>
  },
  labels: {
    supervisor: <optional string>
    advisor: <optional string>
    manager: <optional string>
    organization: <optional string>
    performerName: <optional string>
    performerAge: <optional number>
    counted: <optional string>
    timer: <optional string>
    counter: <optional string>
    charter: <optional string>
    division: <optional string>
    room: <optional string>
  },
  tags: <[string]>
  assigner: <string>
  assigned: <string>
  archived: <optional string>
  archivedBy: <optional string>
```


## `<Measurement>`
```
{
  org: <string>
  pinpoint: <string>
  performer: <string>
  chart: <string>
  raw: {
    correct: <optional number>,
    incorrect: <optional number>,
    latency: <optional number>,
    duration: <optional number>,
    recordFloor: <optional number>,
    recordCeiling: <optional number>,
  },
  clientCreationDate: <optional string>
  recorder: <string>
  recordedBy: <string>
  recorded: <string>
  isUserMeasurement: <boolean>,
  measurer: <string>
  measured: <string>
  locked: <optional string>
  lockedBy: <optional string>
```

## `<CreateMeasurementData>`


```
  _id: <optional string>
  clientCreationDate: <optional string>
  measuredDate: <string>                    // ISO date format. The api uses the requests `x-timezone` to account for the requesters timezone.
  correct: <optional number>                // accel - required if a the chart is a frequency chart and the request has no incorrect field
  incorrect: <optional number>              // decel - required if a the chart is a frequency chart and the request has no correct field
  latency: <optional number>                // required if the chart is a latency chart
  duration: <optional number>               // required if the chart is a duration chart
  recordCeiling: <optional number>
  recordFloor: <optional number>
```

# Endpoints

## Sign In

Sign into a account.

### Endpoint

POST `/api/v2/signin`

### Body

```
  userIdentifier: <string>      // User's email, or username
  password: <string>
```

### Response

```
  accessToken: <string>             // JWT token, store this and use it for future requests
  authorization: <Authorization>    // Associated authorization for the logged in user (for the specific organization)
  user: <User>                      // User that logged in
  userId: <string>                  // User Id that is logged in
  orgId: <string>                   // Organization Id that is logged in
```

## Get Performers

Returns list of performers the authorized user has access within the organization.

### Endpoint

GET `/api/v2/org/:orgId/performer`

### Response

```
  msg: <string>
  orgId: <string>
  performers: <[Performer]>
```

## Get Performer's Charts

Returns a list of charts that the authorized user has access to within the organization, for the specific performer.

### Endpoint

GET `/api/v2/org/:orgId/performer/:performerId/chart`

### Response
```
  msg: <string>
  orgId: <string>
  performerId: <string>
  charts: <[Chart]>
```


## Create Measurement

Creates measurement for the specified performer's chart.

### Endpoint

POST `/api/org/:orgId/chart/:chartId/measurement`

### Body
```
  measurements: <[CreateMeasurementData]>
```

### Response
```
  orgId: <string>
  chartId: <string>
  measurements: <[Measurement]>
```
