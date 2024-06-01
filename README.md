1. Example of builder service
```java
ConvertedDocument convertedPdf = DocumentConverter.from(DocumentType.PNG)
                                                  .to(DocumentType.Pdf)
                                                  .convert();
```

2. Example of Rest Controller
```java

// inject bean
private final DomainHandler domainHanlder;

@PostMapping("/api/books")
public ResponseEntity<GenericResponse> createBooks(@Valid @RequestBody BookCreated bookCreated) {
    DomainPrcoessResponse response = domainHandler.handle(bookCreated);
    return EntityResponseCreator.from(response).create();
} 

```

3. Example of Domain class in DDD
```java
@Builder
public record BookCreated(
    BookTitle title,
    BookAuthor author,
    BookSize size) implements Domain {
}

```

3. Example of Service in layered-based architecture
```java

// inject bean
private final BookRepository bookRepository;

public BookDto createBook(BookDto book) {
  Book bookEntity = book.toEntity();
  Book createdBookEntity = bookResponsity.save(bookEntity);
  return createdBookEntity.toDto();
} 
```

4. Example of BookDto in layered-based architecture
```java
@Builder
public record BookDto(String title, String author, int size) {
  public Book toEntity() {
      return BookEntity.builder().title(title).author(author).size(size).build();
  }
}
```
