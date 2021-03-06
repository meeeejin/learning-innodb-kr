# On learning InnoDB: A journey to the core 한글 번역 :kr:

이 문서는 [On learning InnoDB: A journey to the core](https://blog.jcole.us/innodb/)를 한글 번역한 문서입니다. 이 문서는 개인적인 학습 및 사용을 목적으로, 원저자([Jeremy Cole](https://blog.jcole.us/))의 허가 하에 번역한 문서입니다. [제 블로그](https://meeeejin.github.io/)에서도 확인하실 수 있습니다. :smile:

>  본 문서는 개인적인 필요에 의해 번역한 문서로, 원문을 직역 및 의역하다 보니 번역에 문제가 있을 수 있습니다. 본 문서보다 원문을 먼저 보시는 걸 권장합니다. 현재 버전은 draft이기 때문에 끊임없이 수정 중입니다. 번역과 관련한 문제점 및 수정 제안은 언제든지 환영합니다.

1. [InnoDB 핵심을 이해하기 위한 여행 시작](a-journey-to-the-core/1.on-learning-innodb-a-journey-to-the-core.md): `innodb_ruby` 및 `innodb_diagrams` 프로젝트 소개

2. [innodb_ruby 빠르게 훑어보기](a-journey-to-the-core/2.a-quick-introduction-to-innodb-ruby.md): `innodb_ruby` 설정 방법 및 기능 데모

3. [Space 파일 레이아웃 기초](a-journey-to-the-core/3.the-basics-of-innodb-space-file-layout.md): InnoDB가 space 파일 및 그 안의 페이지들을 어떻게 구성하는지 소개

4. [InnoDB Space 파일의 페이지 관리 방법](a-journey-to-the-core/4.page-management-in-innodb-space-files.md): Space 파일 내의 파일 segment, extent 및 페이지 관리와 관련된 구조 소개

5. [innodb_ruby로 InnoDB의 페이지 관리 방법 직접 확인해보기](a-journey-to-the-core/5.exploring-innodb-page-management-with-innodb_ruby.md): 실제 InnoDB Space 파일의 페이지 관리 데이터 구조를 인터랙티브하게 탐색

6. [InnoDB 인덱스 페이지의 물리적 (physical) 구조](): InnoDB 인덱스 페이지에 대한 설명, 데이터가 저장되는 장소 및 레코드가 저장되는 방법 소개

7. [InnoDB의 B+Tree 인덱스 구조](): InnoDB B+Tree 인덱스와 그 효율성에 대해 논리적으로, 하이 레벨에서 탐색

8. [InnoDB 레코드의 물리적 (physical) 구조](): InnoDB가 행 (row)을 저장하는 형식을 로우 레벨로 설명

9. [페이지 디렉토리를 사용해 InnoDB B+Tree를 효율적으로 탐색하기](): InnoDB에서 B+Tree를 탐색할 때의 효율성을 자세히 확인

10. [InnoDB 데이터 스토리지에 대한 연구 중에 발견된 InnoDB 버그](): 연구 중에 발견된 7가지 버그에 대한 설명

11. [Primary Key가 없는 경우, InnoDB는 어떻게 동작할까?](): 적절한 Primary Key가 없는 테이블에서 사용되는, InnoDB의 암시적인 (implicit) ROW_ID 컬럼에 대한 논의

12. [InnoDB Tidbit: Doublewrite 버퍼가 32 페이지를 낭비함 (512 KiB)](): InnoDB에서 사용하는 파일 segment 할당 방식 소개 및 InnoDB system tablespace에서 512 KiB를 낭비하게 만든 게으른 프로그래밍 방식에 대한 논의

13. [InnoDB UNDO 로깅 및 히스토리 시스템 기초](): 다중 버전 동시성 제어 (multi-version concurrency control, MVCC), undo 로깅, InnoDB의 히스토리 시스템에 대한 소개 및 이들이 서로 어떤 연관이 있는지에 대해 간단히 설명

14. [InnoDB 멀티 버전 관리 (multi-versioning)에 대한 재밌는 사실](): 멀티 버전 및 InnoDB 히스토리 시스템의 "숨겨진" 효과에 대한 흥미로운 사실 소개

15. [더 작은 페이지 크기를 갖는 InnoDB는 최대 6%의 디스크 공간을 낭비한다](): 각 extent에 대한 InnoDB의 필수 부기 정보는 많은 페이지를 낭비하고, 페이지 크기를 줄이면 (4K 또는 8K로) 더 많이 낭비한다는 사실 소개

16. [InnoDB에서 ordered vs. random 인덱스 삽입의 영향을 시각화하기](): `innodb_space`의 `space-lsn-age-illustrations` 및 `space-extents-using` 모드를 사용하여 인덱스 생성 방식의 효율성을 시각화

---

These posts are translated into Korean with the original author’s ([Jeremy Cole](https://blog.jcole.us/)) permission. Thanks for his effort and sharing.

## Original posts

1. [On learning InnoDB: A journey to the core](https://blog.jcole.us/2013/01/02/on-learning-innodb-a-journey-to-the-core/)

2. [A quick introduction to innodb_ruby](https://blog.jcole.us/2013/01/03/a-quick-introduction-to-innodb-ruby/)

3. [The basics of InnoDB space file layout](https://blog.jcole.us/2013/01/03/the-basics-of-innodb-space-file-layout/)

4. [Page management in InnoDB space files](https://blog.jcole.us/2013/01/04/page-management-in-innodb-space-files/)

5. [Exploring InnoDB page management with innodb_ruby](https://blog.jcole.us/2013/01/05/exploring-innodb-page-management-with-innodb_ruby/)

6. [The physical structure of InnoDB index pages](https://blog.jcole.us/2013/01/07/the-physical-structure-of-innodb-index-pages/)

7. [B+Tree index structures in InnoDB](https://blog.jcole.us/2013/01/10/btree-index-structures-in-innodb/)

8. [The physical structure of records in InnoDB](https://blog.jcole.us/2013/01/10/the-physical-structure-of-records-in-innodb/)

9. [Efficiently traversing InnoDB B+Trees with the page directory](https://blog.jcole.us/2013/01/14/efficiently-traversing-innodb-btrees-with-the-page-directory/)

10. [InnoDB bugs found during research on InnoDB data storage](https://blog.jcole.us/2013/04/09/innodb-bugs-found-during-research-on-innodb-data-storage/)

11. [How does InnoDB behave without a Primary Key?](https://blog.jcole.us/2013/05/02/how-does-innodb-behave-without-a-primary-key/)

12. [InnoDB Tidbit](https://blog.jcole.us/2013/05/05/innodb-tidbit-the-doublewrite-buffer-wastes-32-pages-512-kib/)

13. [The basics of the InnoDB undo logging and history system](https://blog.jcole.us/2014/04/16/the-basics-of-the-innodb-undo-logging-and-history-system/)

14. [A little fun with InnoDB multi-versioning](https://blog.jcole.us/2014/04/16/a-little-fun-with-innodb-multi-versioning/)

15. [InnoDB with reduced page sizes wastes up to 6% of disk space](https://blog.jcole.us/2014/05/14/innodb-with-reduced-page-sizes-wastes-up-to-6-of-disk-space/)

16. [Visualizing the impact of ordered vs. random index insertion in InnoDB](https://blog.jcole.us/2014/10/02/visualizing-the-impact-of-ordered-vs-random-index-insertion-in-innodb/)
