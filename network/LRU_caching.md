## LRU 캐싱

LRU (Least Recently Used) 캐싱은 캐시에서 가장 오래 전에 사용된 데이터를 삭제하는 알고리즘이다. 이 알고리즘은 캐시의 크기가 한정되어 있을 때, 캐시에 새로운 데이터가 삽입될 때마다 가장 오래전에 사용된 데이터를 삭제하여 캐시의 용량을 유지하면서 캐시의 효율성을 높일 수 있다.

만약, 캐시에 이미 있는 데이터가 참조되었다면, 해당 데이터의 위치를 가장 최근으로 업데이트한다. 이렇게 함으로써, 최근에 참조된 데이터를 빠르게 접근할 수 있도록 하며, 자주 사용되지 않은 데이터는 캐시에서 삭제된다.

LRU 캐싱은 **캐시의 크기가 한정되어 있을 때**, 캐시에서 가장 오래전에 사용된 데이터를 삭제함으로써 캐시의 공간을 효율적으로 관리할 수 있다. 그러나, 캐시의 크기가 계속해서 증가하거나, 데이터의 접근 패턴이 예측하기 어렵거나, 최근에 사용된 데이터와 오래된 데이터의 구분이 모호한 경우, LRU 캐싱의 효율성이 감소할 수 있다.