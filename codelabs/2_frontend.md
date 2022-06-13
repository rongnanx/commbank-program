# Frontend

## Update The Goal Model

```ts
// types.ts

export interface Goal {
  iconName: string
}
```

## Update The Card Component

```ts
// GoalCard.tsx

export default function GoalCard(props: Props) {
  const dispatch = useAppDispatch()

  const goal = useAppSelector(selectGoalsMap)[props.id]

  const onClick = (event: React.MouseEvent) => {
    event.stopPropagation()
    dispatch(setContentRedux(goal))
    dispatch(setTypeRedux('Goal'))
    dispatch(setIsOpenRedux(true))
  }

  const asLocaleDateString = (date: Date) => new Date(date).toLocaleDateString()

  return (
    <Container key={goal.id} onClick={onClick}>
      <TargetAmount>${goal.targetAmount}</TargetAmount>
      <TargetDate>{asLocaleDateString(goal.targetDate)}</TargetDate>
      <Icon>{goal.iconName}</Icon>
    </Container>
  )
}
```

## Wire In Emoji Picker

### Install `EmojiMart` @[v3.0.1](https://github.com/missive/emoji-mart/tree/v3.0.1)

```shell
npm install --save emoji-mart@3.0.1
```

### Create Reusable Component

```ts
// EmojiPicker.tsx

import { BaseEmoji, Picker } from 'emoji-mart'
import 'emoji-mart/css/emoji-mart.css'
import { useAppSelector } from '../../store/hooks'
import { selectMode } from '../../store/themeSlice'

type Props = { onClick: (emoji: BaseEmoji, event: React.MouseEvent) => void }

export default function EmojiPicker(props: Props) {
  const theme = useAppSelector(selectMode)

  return (
    <Picker
      theme={theme}
      showPreview={false}
      showSkinTones={false}
      onClick={props.onClick}
      color="primary"
    />
  )
}
```

### Display Emoji Picker Conditionally

```ts
// GoalManager.tsx

export function GoalManager(props: Props) {
  const [emojiPickerIsOpen, setEmojiPickerIsOpen] = useState(false)

  const hasIcon = () => icon != null

  const pickEmojiOnClick = (emoji: BaseEmoji, event: MouseEvent) => {
    // TODO(stop event propogation)
    // TODO(set icon locally)
    // TODO(close emoji picker)
    // TODO(create updated goal)
    // TODO(update store)
    // TODO(update database)
  }

  return (
    <EmojiPickerContainer
      isOpen={emojiPickerIsOpen}
      hasIcon={hasIcon()}
      onClick={(event) => event.stopPropagation()}
    >
      <EmojiPicker onClick={pickEmojiOnClick} />
    </EmojiPickerContainer>
  )
}

type EmojiPickerContainerProps = { isOpen: boolean; hasIcon: boolean }

const EmojiPickerContainer = styled.div<EmojiPickerContainerProps>`
  display: ${(props) => (props.isOpen ? 'flex' : 'none')};
  position: absolute;
  top: ${(props) => (props.hasIcon ? '10rem' : '2rem')};
  left: 0;
`
```

### Implement On Click

```ts
const pickEmojiOnClick = (emoji: BaseEmoji, event: MouseEvent) => {
  event.stopPropagation()

  setIcon(emoji.native)
  setEmojiPickerIsOpen(false)

  const updatedGoal: Goal = {
    ...props.goal,
    iconName: emoji.native ?? props.goal.iconName,
    name: name ?? props.goal.name,
    targetDate: targetDate ?? props.goal.targetDate,
    targetAmount: targetAmount ?? props.goal.targetAmount,
  }

  dispatch(updateGoalRedux(updatedGoal))

  updateGoalApi(props.goal.id, updatedGoal)
}
```

## Update The Goal Manager

- [ ] User can add icon
- [ ] User can change icon

### `Case 1`: Goal Has No Icon

#### Add An `AddIcon` Button That Is Only Visible When There Is No Icon

```ts
// GoalManager.tsx

export function GoalManager(props: Props) {
  const [icon, setIcon] = useState<string | null>(null)

  useEffect(() => {
    setIcon(props.goal.iconName)
  }, [props.goal.id, props.goal.iconName])

  const hasIcon = () => icon != null

  const addIconOnClick = (event: React.MouseEvent) => {
    event.stopPropagation()
    setEmojiPickerIsOpen(true)
  }

  return (
    <AddIconButtonContainer hasIcon={hasIcon()}>
      <TransparentButton onClick={addIconOnClick}>
        <FontAwesomeIcon icon={faSmile} size="2x" />
        <AddIconButtonText>Add icon</AddIconButtonText>
      </TransparentButton>
    </AddIconButtonContainer>
  )
}
```

#### `Case 2`: Goal Has Icon

#### Display The Icon When It Is Available

```ts
// GoalModal.tsx

type GoalIconContainerProps = { shouldShow: boolean }

const GoalIconContainer = styled.div<GoalIconContainerProps>`
  display: ${(props) => (props.shouldShow ? 'flex' : 'none')};
`

export function GoalManager(props: Props) {
  const hasIcon = () => icon != null

  const goal = useAppSelector(selectGoalsMap)[props.goal.id]

  return (
    <GoalIconContainer shouldShow={hasIcon()}>
      <GoalIcon icon={goal.iconName} />
    </GoalIconContainer>
  )
}
```

#### Open The Emoji Picker On Click

```ts
// GoalModal.tsx

type GoalIconContainerProps = { shouldShow: boolean }

const GoalIconContainer = styled.div<GoalIconContainerProps>`
  display: ${(props) => (props.shouldShow ? 'flex' : 'none')};
`

export function GoalManager(props: Props) {
  const hasIcon = () => icon != null

  const goal = useAppSelector(selectGoalsMap)[props.goal.id]

  return (
    <GoalIconContainer shouldShow={hasIcon()}>
      <GoalIcon icon={goal.iconName} onClick={addIconOnClick} />
    </GoalIconContainer>
  )
}
```