# Part 3 - Stretch

## Description:

Currently, we allow tags to be added to a conversation, so we can help users to find things they're interested in.

We would like to extend the functionality, to allow the sender of a message to add or update tags on a single message, and allow other users to find messages based on these tags.

While we don't expect everyone to complete this part of the exercise, it will form the basis of disucssion in an interview. Please make as much progress with this, as you feel comfortable doing. Don't allow it to be all-consuming. A couple of hours at most for all parts of the exercise.

We'd love to hear about

- How you would go about implementing the solution
- What problems you might encounter
- How you would go about testing
- What you might do differently

## Solution Notes

sift + command F => search tags

- Update the ChatMessageModel with a tags field
- Create a mutation to add and delete tags
- Create a query to find the messages

At this stage as I don't understand the full logic I wouldn't be able to implement it very well.
I would be copying the implementation of the conversation tag system.

For testing I would do the same but i understand the logic a little better.
on a high level I would Mock the message tag data data and include it in message.logic.spec.ts class declaration `MockedMessageData`.

I would probably use the Graph interface to create the queries and mutations - needs further study.

```
updateTags(
    conversationId: string,
    tags: Tag[],
  ): Promise<ChatConversationModel> {
    throw new Error('Method not implemented');
  }

```

The actual test should look something like this:
The actual data is 'mocked' by jest and the addTags function is called with expect.

At this stage I wouldn't do anything different. If the tests are working then

```
describe('addTags', () => {
  it('should add tags for a message', async () => {
    const tag1 = new Tag();
    tag1.id = 'tag1';
    tag1.type = TagType.subTopic;
    const tag2 = new Tag();
    tag2.id = 'tag2';
    tag2.type = TagType.subTopic;
    const tags = [tag1, tag2];
    const mockChat: ChatChatModel = {
      id: '321b1a570ff321b1a570ff01',
      product: Product.community,
      context: [],
      memberIds: [],
      permissions: [],
      blockedMemberIds: [],
      lastMessageId: new ObjectID(),
      pinnedMessages: [new ObjectID()],
      tags: tags,
    };

    const spy = jest
      .spyOn(conversationData, 'addTags')
      .mockImplementationOnce(() => {
        return Promise.resolve(mockMessage);
      });

    const updatedRecord = await service.updateTags(
      '321b1a570ff321b1a570ff01',
      tags,
    );

    expect(spy).toBeCalledWith('321b1a570ff321b1a570ff01', tags);
    expect(updatedTag).toEqual(mockMessage);
  });
})
```

### src/conversation/models/conversation.model.ts

lines 47 to 53 appears to have the tags implementation for the tags feature.

```
@Prop({
    type: [{ id: { type: String }, type: { type: String } }],
    required: false,
    default: [],
  })
  tags?: Tag[];

@Prop() define property in in MongoDB document
 id: { type: String } value is an object of strings
type: {type: string} value is an object of strings
required: false
default: [] sets value to empty array if none is provided
tags?: string[]; This is the TypeScript declaration for the tags property and is optional
```

### conversation.controller.ts

line 75 to 84

```
  @Put(':conversationId/tags')
  @ApiSecurity('X-API-KEY')
  @UseGuards(XApiKeyGuard)
  @ApiBody({ type: [Tag] })
  async updateTags(
    @Param('conversationId') conversationId: string,
    @Body() tags: Tag[],
  ): Promise<ChatConversation> {
    return this.conversationLogic.updateTags(conversationId, tags);
  }

 Looks like a series of decorators as a kind of pipeline
 @Put http put to api endpoint /conversations/:conversationId/tags
 @ApiSecurity attaches an api-key
 @UseGuards probably makes sure key is not exposed
 @ApiBody contains the tag to be added and should be an array Tag objects
Then the function is declared updateTags() uses the conversation ID and body to use in function.
returns a promise with a resoved type ChatConversation.

```

### conversations.data.ts

line 45 to 48
Method declaration in a in an Interface IConversationData that a class of this interface will provide

```
updateTags(
  conversationId: string,
  tags: Tag[],
): Promise<ChatConversationModel>;

```

t
