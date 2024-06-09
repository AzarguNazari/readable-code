1. Example of builder service
```java

public record Document(
  DocumentType type,
  byte[] content
) {}

Document convertedPdf = DocumentConverter.from(DocumentType.PNG)
                                         .to(DocumentType.PDF)
                                         .convert();
```


2. Example of Rest Controller
```java

@Builder
public record BookCreated(
    BookTitle title,
    BookAuthor author,
    BookSize size) implements Domain {
}


// inject bean
private final DomainHandler domainHanlder;

@PostMapping("/api/books")
public ResponseEntity<BookDto> createBooks(@Valid @RequestBody BookCreated bookCreated) {
    BookDto createdBook = domainHandler.handle(bookCreated);
    return ResponseEntity.ok(createdBook);
} 

```

4. Example of Service in layered-based architecture
```java

// inject bean
private final BookRepository bookRepository;

public BookDto createBook(BookDto book) {
  Book bookEntity = book.toEntity();
  Book createdBookEntity = bookResponsity.save(bookEntity);
  return createdBookEntity.toDto();
} 
```

5. Example of BookDto in layered-based architecture
```java
@Builder
public record BookDto(String title, String author, int size) {
  public Book toEntity() {
      return BookEntity.builder().title(title).author(author).size(size).build();
  }
}

@Entity
@Builder
public record Book(
  @Id Long id,
  String title,
  String author,
  int size,
  LocalDateTime created) {

  public BookDto toDto() {
    return BookDto.builder()
           .title(title)
           .author(author)
           .size(size)
           .build();
  }

}
```


```java
Spring Jpa

public interface BookRepository implements JpaRepository<Book, Long> {
   List<Book> findAll();
   Optional<Book> findById(Long id);
   List<Book> findAllByTitle(String title);
   List<Book> findAllByTitleAndAuthor(String title, String author);
   List<Book> findAllByTitleOrAuthor(String title, String author);
   List<Book> findAllDistinctByTitleAndAuthor(String title, String author);
   List<Book> findAllCreatedBetween(LocalDateTime start, LocalDateTime end);
   List<Book> findAllBySizeLessThan(int size);
   List<Book> findAllBySizeLessThanEqual(int size);
   List<Book> findAllByCreatedAfter(LocalDateTime date);
   List<Book> findAllByCreatedBefore(LocalDateTime date);
   List<Book> findAllByAuthorIsNull();
   List<Book> findAllByAuthorIsNotNull();
   List<Book> findAllByAuthorLike(String title);
   List<Book> findAllByAuthorNotLike(String title);
   List<Book> findAllByAuthorStartingWith(String starting);
   List<Book> findAllByAuthorEndingWith(String ending);
   List<Book> findAllByAuthorContaining(String author);
   List<Book> findAllByAuthorOrderByCreatedDesc(String created);
}

```



```
Clean Architecture

/infrastructure/jpa/entities
@Table("books")
record BookEntity(
   @Id Long id,
   String author
);

/infrastructure/jpa/respositories
@Respository
interface BookEntityRepository implements JpaRepository<BookEntity, Long> {}

/infrastructure/adapters
@Component
class BookRepositoryImpl implements BookRespository {

  private final BookEntityRepository bookEntityRepository;

  public BookRepositoryImpl(BookEntityRepository bookEntityRepository) {
    this.bookEntityRepository = bookEntityRepository;
  }

  @Override
  void save(Book book) {
     BookEntity bookEntity = book.toEntity();
     bookEntityRepository.save(bookEntity);
  }

  @Override
  List<Book> findAll() {
    List<BookEntity> bookEntities = bookEntityRepository.findAll();
    return bookEntities.map(bookEntity::toDto).toList();
  }
}

/adapters/services/BookQueryServiceImpl.java
@Service
class BookQueryServiceImpl implements BookQueryService {
    // overrides and implements the methods
}

/adapters/handlers/BookCreatedHandler.java
@Component
class BookCreatedHandler implements DomainHandler<BookCreated> {

   DomainResponse handle(BookCreated bookCreated) {
      // handles the book created domain event
   }

   @Override
   DomainResponse verify(BookCreated bookCreted) {
      // verify the domain here
  }
}

/core/enitities/book.java
record Book(
  String author
) { }

/core/respositories/BookRespository.java
interface BookRespository {
  void save(Book book);
  List<Book> findAll();
}

/core/services/BookQueryService.java
interface BookQueryService {
    List<Book> findAll();
    List<Book> findAllBookFor(BookTitle bookTitle);
}

```






