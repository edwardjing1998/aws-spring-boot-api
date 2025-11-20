@Transactional
public int deleteAllForClientAndSysPrin(String client, String sysPrin) {
    return sysPrinRepository.deleteByClientAndSysPrin(client, sysPrin);
}
