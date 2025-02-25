```cpp
#ifndef ANDROID_VECTOR_H
#define ANDROID_VECTOR_H

#include <stdint.h>
#include <sys/types.h>

#include “TypeHelpers.h”
#include “VectorImpl.h”

namespace android {

template <class TYPE>
class Vector : private VectorImpl
{
   … 생략
```

`Android vector`도 흔히 사용하는 standard vector와 인터페이스는 크게 다른점이 없다. (그냥 길어서 코드도 생략했다) 템플릿을 쓰기때문에 코드 사이즈를 줄이기 위해, 구체적인 구현은 `VectorImpl`로 옮겨서 상속을 받는 점 정도를 주목한다.

살펴볼만한 점은 내부에서 데이터를 가지는 방식인데, Android vector는 `SharedBuffer`라는 녀석을 따로 두어서 그곳을 저장공간으로 삼는다.

```cpp
namespace android {

class SharedBuffer
{
public:

    /* flags to use with release() */
    enum {
        eKeepStorage = 0x00000001
    };

    /*! allocate a buffer of size ‘size’ and acquire() it.
     *  call release() to free it.
     */
    static          SharedBuffer*           alloc(size_t size);

    /*! free the memory associated with the SharedBuffer.
     * Fails if there are any users associated with this SharedBuffer.
     * In other words, the buffer must have been release by all its
     * users.
     */
    static          void                    dealloc(const SharedBuffer* released);

    //! access the data for read
    inline          const void*             data() const;

    //! access the data for read/write
    inline          void*                   data();

    //! get size of the buffer
    inline          size_t                  size() const;

    //! get back a SharedBuffer object from its data
    static  inline  SharedBuffer*           bufferFromData(void* data);

    //! get back a SharedBuffer object from its data
    static  inline  const SharedBuffer*     bufferFromData(const void* data);

    //! get the size of a SharedBuffer object from its data
    static  inline  size_t                  sizeFromData(const void* data);

    //! edit the buffer (get a writtable, or non-const, version of it)
                    SharedBuffer*           edit() const;

    //! edit the buffer, resizing if needed
                    SharedBuffer*           editResize(size_t size) const;

    //! like edit() but fails if a copy is required
                    SharedBuffer*           attemptEdit() const;

    //! resize and edit the buffer, loose it’s content.
                    SharedBuffer*           reset(size_t size) const;

    //! acquire/release a reference on this buffer
                    void                    acquire() const;

    /*! release a reference on this buffer, with the option of not
     * freeing the memory associated with it if it was the last reference
     * returns the previous reference count
     */
                    int32_t                 release(uint32_t flags = 0) const;

    //! returns wether or not we’re the only owner
    inline          bool                    onlyOwner() const;

private:
        inline SharedBuffer() { }
        inline ~SharedBuffer() { }
        SharedBuffer(const SharedBuffer&);
        SharedBuffer& operator = (const SharedBuffer&);

        // Must be sized to preserve correct alignment.
        mutable std::atomic<int32_t>        mRefs;                // 레퍼런스 카운터!
                size_t                      mSize;
                uint32_t                    mReserved;
public:
        // mClientMetadata is reserved for client use.  It is initialized to 0
        // and the clients can do whatever they want with it.  Note that this is
        // placed last so that it is adjcent to the buffer allocated.
                uint32_t                    mClientMetadata;
};
```

이것은 `Copy on Write`라는 방식을 사용하기 위해서인데, `Lazy copy`라고도 부른다.

```cpp
Vector<int> v1;
Vector<int> v2 = v1;
```

과 같은 대입연산이 이루어질때 v1과 v2는 같은 ShareBuffer를 가리키게 되고 SharedBuffer의 RefCount만 증가하는 것이다. **복사는 당장에 일어나지 않는다.** (생각해보면 vector의 복사는 굉장히 비용이 많이 든다는걸 알 수 있다)

그러다가 ShareBuffer의 내용이 변할때(즉, v1이나 v2에서 내용을 수정할때) 그때서야 ShareBuffer가 Copy되면서 vector의 컨테이너가 두 개로 나뉜다. **변화가 없다면 굳이 복사에 드는 오버헤드를 지불할 필요가 없다**는 생각인 것이다. 괜찮은 생각이다. 아니 훌륭한 생각이다.

VectorImpl을 보면 modern c++ 문법인 `trivial`과 관련된 값들이 보이는데, Vector는 템플릿으로 구성되어 primitive 외에도 객체를 받을 수 있기에 이런 내용이 존재한다.

```cpp
namespace android {

class VectorImpl
{
public:
    enum { // flags passed to the ctor
        HAS_TRIVIAL_CTOR    = 0x00000001,
        HAS_TRIVIAL_DTOR    = 0x00000002,
        HAS_TRIVIAL_COPY    = 0x00000004,
    };
```

`trivial`하다는 것은 단어의 뜻처럼 객체의 복사생성자에서 특별히 아무일도 하지 않음을 의미한다.

`SharedBuffer`는 `operation new`로 공간만 할당되어 있기 때문에 필요한 경우 `placement new`를 통해 생성자를 불러주어야 제대로 동작한다. 템플릿 인자를 통해 들어온 타입을 `type_traits<TYPE>::has_trivial_copy`나 `has_trivial_dtor` 등을 통해, **자명한(trivial) 복사생성자가 있는지**를 파악하여 Android vector는 그것을 처리한다. 그리고 마지막으로 Android vector에는 `Sorted Vector`, `KeyedVector`라는 녀석들도 존재하는데 이것은 heap이나 map같은 녀석이라고 보면 될 것 같다.
