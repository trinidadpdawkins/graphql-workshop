![ChilliCream](docs/images/ChilliCream.svg)

# Getting started with GraphQL on ASP.NET Core and Hot Chocolate - Workshop

If you want to browse the GraphQL server head over [here](https://aspnetcorews-backend.azurewebsites.net).

## Prerequisites

For this workshop we need a couple of prerequisites. First, we need the [.NET Core SDK 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

Then we need some IDE/Editor in order to do some proper C# coding, you can use [VSCode](https://code.visualstudio.com/) or if you have already on your system Visual Studio or JetBrains Rider.

Last but not least we will use our GraphQL IDE [Banana Cakepop](https://hotchocolate.io/docs/banana-cakepop).

> Note: When installing Visual Studio you only need to install the `ASP.NET and web development` workload.

## What you'll be building

In this workshop, you'll learn by building a full-featured GraphQL Server with ASP.NET Core and Hot Chocolate from scratch. We'll start from File/New and build up a full-featured GraphQL server with custom middleware, filters, subscription and relay support.

**Database Schema**:

![Database Schema Diagram](docs/images/21-conference-planner-db-diagram.png)

**GraphQL Schema**:

```graphql
schema {
  query: Query
  mutation: Mutation
  subscription: Subscription
}

type Query {
  attendeeById(id: ID!): Attendee!
  attendeesAsync(after: String before: String first: PaginationAmount last: PaginationAmount): AttendeeConnection
  attendeesById(ids: [ID]): [Attendee!]!
  node(id: ID!): Node
  sessionById(id: ID!): Session!
  sessions(after: String before: String first: PaginationAmount last: PaginationAmount order_by: SessionSort where: SessionFilter): SessionConnection
  sessionsById(ids: [ID]): [Session!]!
  speakerById(id: ID!): Speaker!
  speakers(after: String before: String first: PaginationAmount last: PaginationAmount): SpeakerConnection
  speakersById(ids: [ID]): [Speaker!]!
  trackById(id: ID!): Track!
  trackByName(name: String!): Track!
  trackByNames(names: [String!]!): [Track!]!
  tracks(after: String before: String first: PaginationAmount last: PaginationAmount): TrackConnection
}

type Mutation {
  addSession(input: AddSessionInput!): AddSessionPayload!
  addSpeaker(input: AddSpeakerInput!): AddSpeakerPayload!
  addTrack(input: AddTrackInput!): AddTrackPayload!
  checkInAttendee(input: CheckInAttendeeInput!): CheckInAttendeePayload!
  modifySpeaker(input: ModifySpeakerInput!): ModifySpeakerPayload!
  registerAttendee(input: RegisterAttendeeInput!): RegisterAttendeePayload!
  renameTrack(input: RenameTrackInput!): RenameTrackPayload!
  scheduleSession(input: ScheduleSessionInput!): ScheduleSessionPayload!
}

type Subscription {
  onAttendeeCheckedInAsync(sessionId: ID!): SessionAttendeeCheckIn!
  onSessionScheduled: Session!
}

"The node interface is implemented by entities that have a global unique identifier."
interface Node {
  id: ID!
}

type AddSessionPayload {
  clientMutationId: String
  errors: [UserError!]!
  session: Session
}

type AddSpeakerPayload {
  clientMutationId: String
  errors: [UserError!]!
  speaker: Speaker
}

type AddTrackPayload {
  clientMutationId: String
  errors: [UserError!]!
  track: Track
}

type Attendee implements Node {
  emailAddress: String
  firstName: String!
  id: ID!
  lastName: String!
  sessions: [Session!]!
  userName: String!
}

"A connection to a list of items."
type AttendeeConnection {
  "A list of edges."
  edges: [AttendeeEdge!]
  "A flattened list of the nodes."
  nodes: [Attendee]
  "Information to aid in pagination."
  pageInfo: PageInfo!
  totalCount: Int!
}

"An edge in a connection."
type AttendeeEdge {
  "A cursor for use in pagination."
  cursor: String!
  "The item at the end of the edge."
  node: Attendee
}

type CheckInAttendeePayload {
  attendee: Session
  clientMutationId: String
  errors: [UserError!]!
}

type ModifySpeakerPayload {
  clientMutationId: String
  errors: [UserError!]!
  speaker: Speaker
}

"Information about pagination in a connection."
type PageInfo {
  "When paginating forwards, the cursor to continue."
  endCursor: String
  "Indicates whether more edges exist following the set defined by the clients arguments."
  hasNextPage: Boolean!
  "Indicates whether more edges exist prior the set defined by the clients arguments."
  hasPreviousPage: Boolean!
  "When paginating backwards, the cursor to continue."
  startCursor: String
}

type RegisterAttendeePayload {
  attendee: Attendee
  clientMutationId: String
  errors: [UserError!]!
}

type RenameTrackPayload {
  clientMutationId: String
  errors: [UserError!]!
  track: Track
}

type ScheduleSessionPayload {
  clientMutationId: String
  errors: [UserError!]!
  session: Session
  speakers: [Speaker!]
  track: Track
}

type Session implements Node {
  abstract: String
  attendees: [Attendee!]!
  duration: TimeSpan!
  endTime: DateTime
  id: ID!
  speakers: [Speaker!]!
  startTime: DateTime
  title: String!
  track: Track
  trackId: ID
}

type SessionAttendeeCheckIn {
  attendee: Attendee!
  attendeeId: ID!
  checkInCount: Int!
  sessionId: ID!
}

"A connection to a list of items."
type SessionConnection {
  "A list of edges."
  edges: [SessionEdge!]
  "A flattened list of the nodes."
  nodes: [Session]
  "Information to aid in pagination."
  pageInfo: PageInfo!
  totalCount: Int!
}

"An edge in a connection."
type SessionEdge {
  "A cursor for use in pagination."
  cursor: String!
  "The item at the end of the edge."
  node: Session
}

type Speaker implements Node {
  bio: String
  id: ID!
  name: String!
  sessions: [Session!]!
  webSite: String
}

"A connection to a list of items."
type SpeakerConnection {
  "A list of edges."
  edges: [SpeakerEdge!]
  "A flattened list of the nodes."
  nodes: [Speaker]
  "Information to aid in pagination."
  pageInfo: PageInfo!
  totalCount: Int!
}

"An edge in a connection."
type SpeakerEdge {
  "A cursor for use in pagination."
  cursor: String!
  "The item at the end of the edge."
  node: Speaker
}

type Track implements Node {
  id: ID!
  name: String!
  sessions: [Session!]!
}

"A connection to a list of items."
type TrackConnection {
  "A list of edges."
  edges: [TrackEdge!]
  "A flattened list of the nodes."
  nodes: [Track]
  "Information to aid in pagination."
  pageInfo: PageInfo!
  totalCount: Int!
}

"An edge in a connection."
type TrackEdge {
  "A cursor for use in pagination."
  cursor: String!
  "The item at the end of the edge."
  node: Track
}

type UserError {
  code: String!
  message: String!
}

input AddSessionInput {
  abstract: String
  clientMutationId: String
  speakerIds: [ID]
  title: String!
}

input AddSpeakerInput {
  bio: String
  clientMutationId: String
  name: String!
  webSite: String
}

input AddTrackInput {
  clientMutationId: String
  name: String!
}

input CheckInAttendeeInput {
  attendeeId: ID!
  clientMutationId: String
  sessionId: ID!
}

input ModifySpeakerInput {
  bio: String
  clientMutationId: String
  id: ID!
  name: String
  webSite: String
}

input RegisterAttendeeInput {
  clientMutationId: String
  emailAddress: String!
  firstName: String!
  lastName: String!
  userName: String!
}

input RenameTrackInput {
  clientMutationId: String
  id: ID!
  name: String!
}

input ScheduleSessionInput {
  clientMutationId: String
  endTime: DateTime!
  sessionId: ID!
  startTime: DateTime!
  trackId: ID!
}

input SessionFilter {
  # fields removed for brevity
}

input SessionSort {
  # fields removed for brevity
}

enum SortOperationKind {
  ASC
  DESC
}

"The `Boolean` scalar type represents `true` or `false`."
scalar Boolean

"The `DateTime` scalar represents an ISO-8601 compliant date time type."
scalar DateTime

"The `ID` scalar type represents a unique identifier, often used to refetch an object or as key for a cache. The ID type appears in a JSON response as a String; however, it is not intended to be human-readable. When expected as an input type, any string (such as `\"4\"`) or integer (such as `4`) input value will be accepted as an ID."
scalar ID

"The `Int` scalar type represents non-fractional signed whole numeric values. Int can represent values between -(2^31) and 2^31 - 1."
scalar Int

scalar PaginationAmount

"The `String` scalar type represents textual data, represented as UTF-8 character sequences. The String type is most often used by GraphQL to represent free-form human-readable text."
scalar String

scalar TimeSpan
```

## Sessions

| Session | Topics |
| ----- | ---- |
| [Session #1](docs/1-creating-a-graphql-server-project.md) | Building a basic GraphQL server API. |
| [Session #2](docs/2-building-out-the-graphql-server.md) | Controlling nullability and understanding DataLoader.  |  |
| [Session #3](docs/3-schema-design.md) | GraphQL schema design approaches. |
| [Session #4](docs/4-understanding-middleware.md) | Understanding middleware. |
| [Session #5](docs/) | Adding complex filter capabilities. |
| [Session #6](docs/) | Getting the GraphQL server production ready. |
