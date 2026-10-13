# ICE Task 3 – Album Collection & Debugging
**Name:** Thandolwethu Mahlangu
**Student number:** ST10527885


## 1. Introduction

This project is an Album Collection application built with React Native. A user can enter an album's title, artist, year, genre and rating, have the input validated, add the album to a favourites collection, view the collection in a list, and remove albums they no longer want.

The task was a debugging exercise rather than a build-from-scratch task. I was given an existing `App.tsx` containing intentional errors. My job was to run the application, find the errors, understand why each one happened, correct them, and document the corrections.

## 2. Development Environment

- **Framework:** React Native with Expo (SDK 54)
- **Language:** TypeScript (`expo-template-blank-typescript`)
- **Development environment:** Institutional VM
- **Emulator:** BlueStacks 5
- **Device testing:** Expo Go running inside BlueStacks 5
- **Version control:** Git and GitHub (private repository)

All application code is contained in `App.tsx`. The only additional dependency is `@react-native-picker/picker`, installed with `npx expo install`.

## 3. Error Log

| # | Location | Problem | Error Type | Correction |
|---|----------|---------|------------|------------|
| 1 | `Album` type | `year` and `rating` were declared as `string`, but `handleSave` stores `Number(year)` and `Number(rating)`. The declared type did not match the values being assigned, so TypeScript reported a type error. | TypeScript | Changed both `year` and `rating` to `number` so the type matches the stored values. |
| 2 | `validateForm` (title length) | The check used `length < MIN_TEXT_LENGTH && length > MAX_TITLE_LENGTH`. A length cannot be below 2 and above 50 at the same time, so the condition was never true and titles of the wrong length were accepted. | Logic | Changed `&&` to `\|\|` so a title that is too short or too long is rejected. |
| 3 | `validateForm` (artist length) | Same mistake as the title check, so invalid artist names were never rejected. | Logic | Changed `&&` to `\|\|`. |
| 4 | `validateForm` (year) | Only the minimum year was checked, although the message says the year must be between 1900 and the current year. Future years such as 3000 were accepted. | Logic / Validation | Added `numericYear > currentYear` to the condition. |
| 5 | `validateForm` (rating) | Only `rating < 1` was checked, so values such as 10 or 99 were accepted even though the maximum is 5. | Logic / Validation | Added `numericRating > MAX_RATING` to the condition. |
| 6 | `handleSave` | `setAlbums([temporaryAlbum])` replaced the entire array, so every new album erased the previous ones and the collection never held more than one album. | State / Logic | Changed to `setAlbums((currentAlbums) => [...currentAlbums, temporaryAlbum])`, which keeps the existing albums and appends the new one. |
| 7 | `handleDelete` | The filter used `album.id === id`, which keeps only the album being deleted and removes all the others. | Logic | Changed to `album.id !== id` so only the selected album is removed. |
| 8 | Genre `Picker` | `selectedValue={title}` bound the picker to the title state instead of the genre state, so the picker did not reflect the selected genre. | State / Runtime | Changed to `selectedValue={genre}`. |
| 9 | Genre `Picker.Item` | `value={genre}` gave every item the same value (the current state) instead of its own genre, so the user could not select a genre properly. | Logic / Runtime | Changed to `value={item}` so each item carries its own genre name. |
| 10 | `FlatList` | `keyExtractor` returned `item.title`, which is not unique. Two albums with the same title produce duplicate keys, causing React warnings and rendering and deletion problems. | FlatList / Runtime | Changed to `item.id`, which is unique for each album. |
| 11 | Picker `.map` block | The JSX mixed tabs and spaces. This does not stop the app from running, but it harms readability and consistency. | Style | Re-indented the block consistently. |
| 12 | Picker import | The supplied import `@expo/ui/community/picker` is not available in Expo SDK 54, so the module could not be resolved and the `onValueChange` parameter had no usable type. | Import / TypeScript | Installed `@react-native-picker/picker` using `npx expo install` and changed the import. Typed the callback parameter as `(value: string)`. |

## 4. Testing

I first ran the original supplied application to observe its behaviour. Albums vanished when a second one was added, and Delete removed every album except the one pressed. After making the corrections, I tested the app in BlueStacks 5 through Expo Go.

- **Test 1 – Initial application:** The app launched, the form and the "No albums have been added yet." message were displayed, and there were no unexpected errors.
- **Test 2 – Invalid input:** I submitted an empty form, a 1-character title, a year of 3000, a rating of 9, and a rating of 2.5. Each was rejected with a validation alert and nothing was added to the list.
- **Test 3 – Valid album:** I entered valid information and pressed "Add to Favourites". The album appeared in the list with the correct title, artist, year, genre and rating, and the form cleared.
- **Test 4 – Multiple albums:** I added three different albums. All three stayed in the list, each showed its own correct information, and the counter in the heading updated.
- **Test 5 – Delete:** I deleted the middle album from a list of three. Only that album disappeared and the other two remained.
- **Test 6 – Repeat:** I kept adding and deleting albums, including two albums with the same title, and the app stayed stable.


## 6. Conclusion

This task taught me that code can compile and still behave incorrectly. Bugs such as `&&` instead of `||`, `===` instead of `!==`, and overwriting state instead of appending to it only showed up when I ran the app and tried different inputs. I also learned that supplied code may depend on packages that do not match the Expo SDK version being used, and that `npx expo install` picks compatible versions. Overall, I learned to read code carefully, trace what each condition and state update actually does, and test both valid and invalid cases instead of assuming the first fix was enough.



